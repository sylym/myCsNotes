## 补充

- make之前要先进入pa_nju文件夹
  - 先make clean再进行测试

## pa1

#### 内存模拟

- ![image-20230314144450692](https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230314144450692.png)
- 使用一个数组来模拟内存（多层读取，在PA3-2涉及）

```c
uint8_t hw_mem[MEM_SIZE_B]; 
uint32_t hw_mem_read(paddr_t paddr, size_t len) {
	uint32_t ret = 0; 
    memcpy(&ret, hw_mem + paddr, len);
    return ret;
}
uint32_t paddr_read(paddr_t paddr, size_t len) {
	uint32_t ret = 0; 
    ret = hw_mem_read(paddr, len); 
    return ret;
}
uint32_t laddr_read(laddr_t laddr, size_t len) {
	return paddr_read(laddr, len); 
}
uint32_t vaddr_read(vaddr_t vaddr, uint8_t sreg, size_t len) {
	assert(len == 1 || len == 2 || len == 4); 
    return laddr_read(vaddr, len);
}
```

### 寄存器模拟

- ![image-20230314150137172](https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230314150137172.png)

- 数据结构

  - ```c
    typedef struct {
        union {
            union {
                union {//前4行寄存器，润许不同方式访问位于不同位置的元素
                    uint32_t _32;
                    uint16_t _16; 
                    uint8_t _8[2];
                }; 
                uint32_t val;//寄存器32位的总值
            } gpr[8];
            struct {
                uint32_t eax, ecx, edx, ebx, esp, ebp, esi, edi;}; //每个变量对应32位空间，正好对应上面的区域划分，使用union类型实现了对不同位置数据的读写
        }; …
    } CPU_STATE;
    ```


### 整数运算和表示

- 返回结果，并设置标志位
  - 结果不足32位时高位补0

```c
uint32_t alu_add(uint32_t src, uint32_t dest, size_t data_size) {//表示参与运算的两个数，以及操作数的长度（8，16，32）
	printf("\e[0;31mPlease implement me at alu.c\e[0m\n"); 		assert(0);
	return 0;
}
```

- 有关ADD指令的描述 261（找到i386⼿册Sec. 17.2.2.11），看Flags Affected: OF, SF, ZF, AF, CF, and PF as described in Appendix C（AF不模拟）
- 例

```c
uint32_t alu_add(uint32_t src, uint32_t dest, size_t data_size) { 
    uint32_t res = 0;
    res = dest + src;
	// 获取计算结果
	set_CF_add(res, src, data_size); // 设置标志位 set_PF(res); 	   // set_AF();
	// 我们不模拟AF
	set_ZF(res, data_size); 
    set_SF(res, data_size); 			
    set_OF_add(res, src, dest, data_size);
	return res & (0xFFFFFFFF >> (32 - data_size)); // ⾼位清零并返回
}
```

#### 标志位的判断

- 对cpu中eflags寄存器的访问 i386⼿册 sec 2.3.4.1

- OF,SF,ZF,CF,PF

  - ![image-20230314171544891](https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230314171544891.png)

  - CF（进位标志） =1 算术操作最高位产生了进位或借位 =0 最高位无进位或借位 ；

    - ```c
      void set_CF_add(uint32_t result, uint32_t src, size_t data_size) {
      	result = sign_ext(result & (0xFFFFFFFF >> (32 - data_size)), data_size);
      	src = sign_ext(src & (0xFFFFFFFF >> (32 - 	data_size)), data_size);//先规格化数据，高位置0
      	cpu.eflags.CF = result < src; //直接cpu.eflags对成员函数进行赋值
      }
      ```

      

  - PF（奇偶标志） =1 数据最低8位中1的个数为偶数; =0 数据最低8位中1的个数为奇数；

  - ZF（零标志） =1 操作结果为0 =0 结果不为0；

    - ```c
      void set_ZF(uint32_t result, size_t data_size) { 	result = result & (0xFFFFFFFF >> (32 - data_size));
      	cpu.eflags.ZF = (result == 0);
      }
      ```

      

  - SF（符号标志） =1 结果最高位为1 =0 结果最高位为0；

    - ```c
      void set_SF(uint32_t result, size_t data_size) { 	result = sign_ext(result & (0xFFFFFFFF >> (32 - data_size)), data_size);
      	cSFpu.eflags.SF = sign(result);//alu.h定义，返回标志位
      }
      ```

  - OF（溢出标志） =1 此次运算发生了溢出 =0 无溢出。

    - ++->-,--->+发生变号

    - ```c
      if(sign(src) == sign(dest)) { 
      	if(sign(src) != sign(result)) 
      		cpu.eflags.OF = 1;
      	else
          	cpu.eflags.OF = 0;
      } else { 
      cpu.eflags.OF = 0;
      }
      ```

      

#### 实现

```c
#include "cpu/cpu.h"
uint32_t mysign(uint32_t x, size_t data_size)
{
    return (x>>(data_size-1))&1;
}
uint32_t set0(uint32_t x, size_t data_size)
{
    return x & (0xFFFFFFFF>>(32-data_size));
}
uint32_t getcom(uint32_t x, size_t data_size)//求-x补码
{
    x=~x;
    return set0(x+1,data_size);
}
void set_OF(uint32_t src, uint32_t dest, uint32_t result, size_t data_size)
{
    result=set0(result,data_size);
    src=set0(src,data_size);
    dest=set0(dest,data_size);
    if(mysign(src,data_size)==mysign(dest,data_size)&&mysign(src,data_size)!=mysign(result,data_size))
    {
        cpu.eflags.OF=1;
    }
    else
    {
        cpu.eflags.OF=0;
    }
    
}
void set_SF(uint32_t x, size_t data_size)
{
    x=set0(x,data_size);
    cpu.eflags.SF=mysign(x,data_size);
}
void set_ZF(uint32_t x, size_t data_size)
{
    x=set0(x,data_size);
    cpu.eflags.ZF=(x==0);
}
void set_CF(uint32_t result, uint32_t src, size_t data_size)
{
    result=set0(result,data_size);
    src=set0(src,data_size);
    cpu.eflags.CF=(result<src);
}
void set_PF(uint32_t x, size_t data_size)
{
    x=set0(x,data_size);
    uint32_t num=0;
    for(uint32_t i=0;i<8;i++)
    {
        if(x>>i&1)
            num++;
    }
    cpu.eflags.PF=(num%2==0);
}
uint64_t set_OC_MUL(uint64_t x,size_t data_size,uint32_t type)
{
    uint32_t res = 0;
    for(int i=0;i<data_size;i++)
    {
        if((((uint64_t)1<<(i+data_size))&x)!=type%2)
        {
            res = 1;
        }
    }
    cpu.eflags.OF = res;
    cpu.eflags.CF = res;
    return x;
}
uint32_t alu_add(uint32_t src, uint32_t dest, size_t data_size)
{
    uint32_t res=0;
    res=dest+src;
    set_OF(src,dest,res,data_size);
    set_SF(res,data_size);
    set_ZF(res,data_size);
    set_CF(res,src,data_size);
    set_PF(res,data_size);
    return set0(res,data_size);
}

uint32_t alu_adc(uint32_t src, uint32_t dest, size_t data_size)
{
    uint32_t res = alu_add(src,cpu.eflags.CF,data_size);
    uint32_t temp_CF = cpu.eflags.CF,temp_OF=cpu.eflags.OF;
    res = alu_add(res,dest,data_size);
    cpu.eflags.CF|=temp_CF;
    cpu.eflags.OF^=temp_OF;
    return set0(res,data_size);
}

uint32_t alu_sub(uint32_t src, uint32_t dest, size_t data_size)
{
    uint32_t res=set0(alu_add(dest,getcom(src,data_size),data_size),data_size);
    src=set0(src,data_size);
    dest=set0(dest,data_size);
    if(src>dest)
        cpu.eflags.CF=1;
    else 
        cpu.eflags.CF=0;
    set_OF(~src,dest,res,data_size);
    return res;
}

uint32_t alu_sbb(uint32_t src, uint32_t dest, size_t data_size)
{
    uint32_t res,temp = alu_add(src,cpu.eflags.CF,data_size);
    uint32_t temp_CF = cpu.eflags.CF, temp_OF=cpu.eflags.OF; 
    res = alu_sub(temp,dest,data_size);
    cpu.eflags.CF |= temp_CF; 
    cpu.eflags.OF ^= temp_OF;
    return res;
}
uint64_t alu_mul(uint32_t src, uint32_t dest, size_t data_size)
{
    src = set0(src,data_size);
    dest = set0(dest,data_size);
    uint64_t res = (uint64_t)src*(uint64_t)dest;
    set_OC_MUL(res,data_size,0);
    set_SF(res,data_size);
    set_ZF(res,data_size);
    set_PF(res,data_size);
    return res & (0xFFFFFFFFFFFFFFFF>>(64-2*data_size));
}

uint64_t sign_ext2_64(uint32_t x, size_t data_size)
{
        assert(data_size == 16 || data_size == 8 || data_size == 32);
        switch (data_size)
        {
        case 8:
                return (int64_t)((int8_t)(x & 0xff));
        case 16:
                return (int64_t)((int16_t)(x & 0xffff));
        default:
                return (int64_t)((int32_t)x);
        }
}
int64_t alu_imul(int32_t src, int32_t dest, size_t data_size)
{
    uint64_t res = 0;
    uint64_t src64=sign_ext2_64(src,data_size),dest64=sign_ext2_64(dest,data_size);
    res = src64*dest64;
    return res;
}

// need to implement alu_mod before testing
uint32_t alu_div(uint64_t src, uint64_t dest, size_t data_size)
{
    src = set0(src,data_size);
    dest = set0(dest,data_size);
    uint32_t res = dest/src;
    set_SF(res,data_size);
    set_ZF(res,data_size);
    set_PF(res,data_size);
    return res;
}

// need to implement alu_imod before testing
int32_t alu_idiv(int64_t src, int64_t dest, size_t data_size)
{
    bool state = 0;
    if(mysign(src,data_size)==1)
    {
        state^=1;
        src = sign_ext2_64(getcom(src,data_size),data_size);
    }
    if(mysign(dest,data_size)==1)
    {
        state^=1;
        dest = sign_ext2_64(getcom(dest,data_size),data_size);
    }
    uint32_t res = dest/src;
    if(state)
        res=getcom(res,data_size);
    return res;
}

uint32_t alu_mod(uint64_t src, uint64_t dest)
{
    return dest%src;
}

int32_t alu_imod(int64_t src, int64_t dest)
{
    return dest%src;
}

uint32_t alu_and(uint32_t src, uint32_t dest, size_t data_size)
{
    src = set0(src,data_size);
    dest = set0(dest,data_size);
    uint32_t res = src&dest;
    set_SF(res,data_size);
    set_ZF(res,data_size);
    set_PF(res,data_size);
    return res;
}

uint32_t alu_xor(uint32_t src, uint32_t dest, size_t data_size)
{
    src = set0(src,data_size);
    dest = set0(dest,data_size);
    uint32_t res = src^dest;
    set_SF(res,data_size);
    set_ZF(res,data_size);
    set_PF(res,data_size);
    return res;
}

uint32_t alu_or(uint32_t src, uint32_t dest, size_t data_size)
{
    src = set0(src,data_size);
    dest = set0(dest,data_size);
    uint32_t res = src|dest;
    set_SF(res,data_size);
    set_ZF(res,data_size);
    set_PF(res,data_size);
    return res;
}

uint32_t alu_shl(uint32_t src, uint32_t dest, size_t data_size)
{
    src = set0(src,data_size);
    dest = set0(dest,data_size);
    for(uint32_t i=0;i<src;i++)
    {
        cpu.eflags.CF=mysign(dest,data_size);
        dest<<=1;
    }
    set_SF(dest,data_size);
    set_ZF(dest,data_size);
    set_PF(dest,data_size);
    return set0(dest,data_size);
}

uint32_t alu_shr(uint32_t src, uint32_t dest, size_t data_size)
{
src = set0(src,data_size);
    dest = set0(dest,data_size);
    for(uint32_t i=0;i<src;i++)
    {
        cpu.eflags.CF=dest&1;
        dest>>=1;
    }
    set_SF(dest,data_size);
    set_ZF(dest,data_size);
    set_PF(dest,data_size);
    return set0(dest,data_size);
}

uint32_t alu_sar(uint32_t src, uint32_t dest, size_t data_size)
{
    src = set0(src,data_size);
    dest = set0(dest,data_size);
    uint32_t temp = mysign(dest,data_size);
    for(uint32_t i=0;i<src;i++)
    {
        cpu.eflags.CF=dest&1;
        dest>>=1;
        dest|=temp<<(data_size-1);
    }
    set_SF(dest,data_size);
    set_ZF(dest,data_size);
    set_PF(dest,data_size);
    return set0(dest,data_size);
}

uint32_t alu_sal(uint32_t src, uint32_t dest, size_t data_size)
{
    return alu_shl(src,dest,data_size);
}

```

### 浮点数的运算和表示	

- 浮点数表示结构

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315172318876.png" alt="image-20230315172318876" style="zoom: 33%;" />

#### 加减法

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315194944400.png" alt="image-20230315194944400" style="zoom:50%;" />
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315172732668.png" alt="image-20230315172732668" style="zoom: 33%;" />

  - 过程：
    - 提取符号、阶码、尾数 
    - 整数运算得到中间结果 
      -  对阶：小阶向大阶看齐 小阶增加至大阶，同时尾数 右移，保证对应真值不变
        - 对接过程中需要移位，而移位会造成精度损失，因此使用保护位提高精度(先向左移3位，会破坏阶数，不过已经提取保存了)
        - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315174051085.png" alt="image-20230315174051085" style="zoom: 33%;" />
      - 尾数相加（相减）
    - 舍入并规格化后返回（加减法中一定有exp>0）
      - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315185155842.png" alt="image-20230315185155842" style="zoom:50%;" />
        - sig_grs>>26=1，说明刚好剩下的是23+3位
        - 阶码上溢变无穷
      - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315185223819.png" alt="image-20230315185223819" style="zoom:50%;" />
        - 额外移动一次是因为exp=0是是-126而不是-127，所以要纠正
      - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315185246389.png" alt="image-20230315185246389" style="zoom:50%;" />
      - 舍入规则：
        - 如果 `G = 0` ，向下舍入（什么都不做）
        - 如果 `G = 1` ， `RS == 10` 或 `RS == 01` ，向上舍入（向尾数添加一个）
        - if `GSR = 111`or`100` ，round to even
        - 舍入若产生尾数加1，有可能出现破坏规格化的情况 •，此时需要进行额外的一次右规并判断阶码上溢的情况


#### 乘除法

-  乘法：尾数相乘，阶码相加
  -  <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316002859115.png" alt="image-20230316002859115" style="zoom: 33%;" />
  -  <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315195624447.png" alt="image-20230315195624447" style="zoom:50%;" />
  -  与约定的26位小数相比，得到的是46位小数，要对exp-20进行修正
-  除法：尾数相除，阶码相减
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315195654881.png" alt="image-20230315195654881" style="zoom:50%;" />
  - 同样由于不一定是26位小数，要通过对exp修正

-  <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230315195800947.png" alt="image-20230315195800947" style="zoom:50%;" />

#### 实现

```c
#include "nemu.h"
#include "cpu/fpu.h"

FPU fpu;
// special values
FLOAT p_zero, n_zero, p_inf, n_inf, p_nan, n_nan;

// the last three bits of the significand are reserved for the GRS bits
inline uint32_t internal_normalize(uint32_t sign, int32_t exp, uint64_t sig_grs)
{

	// normalization
	bool overflow = false; // true if the result is INFINITY or 0 during normalize
    uint32_t sticky=0;
	if ((sig_grs >> (23 + 3)) > 1 || exp < 0)
	{
	    checkagain:
		// normalize toward right
		while ((((sig_grs >> (23 + 3)) > 1) && exp < 0xff) // condition 1
			   ||										   // or
			   (sig_grs > 0x04 && exp < 0)				   // condition 2
			   )
		{
			/* TODO: shift right, pay attention to sticky bit*/
            sticky = sticky|(sig_grs&0x1);
            sig_grs>>=1;
            sig_grs|=sticky;
            ++exp;
		}

		if (exp >= 0xff)//阶码全一，尾数置零，表示无穷
		{
			/* TODO: assign the number to infinity */
            sig_grs = 0;
            exp = 0xff;
		}
		if (exp == 0)
		{
			// 有一个非规格化数，阶码为0，但表示的是2的-126次方。因此，尾数应该再向右移动一次。
			/* TODO: shift right, pay attention to sticky bit*/
            sticky = sticky|(sig_grs&0x1);
            sig_grs>>=1;
            sig_grs|=sticky;
		}
		if (exp < 0)
		{
			/* TODO: assign the number to zero */
            sig_grs=0;
            exp=0;
		}
	}
	else if (((sig_grs >> (23 + 3)) == 0) && exp > 0)
	{
		// normalize toward left
		while (((sig_grs >> (23 + 3)) == 0) && exp > 0)
		{
			/* TODO: shift left */
            sig_grs<<=1;
            --exp;
		}
		if (exp == 0)
		{
			// denormal
			/* TODO: shift right, pay attention to sticky bit*/
            sticky = sticky|(sig_grs&0x1);
            sig_grs>>=1;
            sig_grs|=sticky;
		}
	}
	else if (exp == 0 && sig_grs >> (23 + 3) == 1)
	{
		// two denormals result in a normal
		exp++;
	}

	if (!overflow)//舍入
	{
		/* TODO: round up and remove the GRS bits */
        uint32_t G=(sig_grs>>2)&1,R=(sig_grs>>1)&1,S=sig_grs&1;
        if(G==1)
        {
            if((R==1&&S==0)||(R==0&&S==1)||(R==1&&S==1))
            {
                sig_grs+=1<<3;
            }
            else
            {
                if(((sig_grs>>3)&1)==1)
                    sig_grs+=1<<3;
            }
        }
        //破坏规格化的判断
        if((sig_grs >> (23 + 3)) > 1)
        {
            goto checkagain;
        }
        sig_grs>>=3;
	}
	FLOAT f;
	f.sign = sign;
	f.exponent = (uint32_t)(exp & 0xff);
	f.fraction = sig_grs; // here only the lowest 23 bits are kept
	return f.val;
}

CORNER_CASE_RULE corner_add[] = {
	{P_ZERO_F, P_ZERO_F, P_ZERO_F},
	{N_ZERO_F, P_ZERO_F, P_ZERO_F},
	{P_ZERO_F, N_ZERO_F, P_ZERO_F},
	{N_ZERO_F, N_ZERO_F, N_ZERO_F},
	{P_INF_F, N_INF_F, N_NAN_F},
	{N_INF_F, P_INF_F, N_NAN_F},
	{P_INF_F, P_NAN_F, P_NAN_F},
	{P_INF_F, N_NAN_F, N_NAN_F},
	{N_INF_F, P_NAN_F, P_NAN_F},
	{N_INF_F, N_NAN_F, N_NAN_F},
	{N_NAN_F, P_NAN_F, P_NAN_F},
};

// a + b
uint32_t internal_float_add(uint32_t b, uint32_t a)
{
	// corner cases
	int i = 0;
	for (; i < sizeof(corner_add) / sizeof(CORNER_CASE_RULE); i++)
	{
		if (a == corner_add[i].a && b == corner_add[i].b)
			return corner_add[i].res;
	}
	if (a == P_ZERO_F || a == N_ZERO_F)
	{
		return b;
	}
	if (b == P_ZERO_F || b == N_ZERO_F)
	{
		return a;
	}

	FLOAT f, fa, fb;
	fa.val = a;
	fb.val = b;
	//test 
    //FLOAT t;
    //t.fval = fa.fval + fb.fval;
    
	// infity, NaN
	if (fa.exponent == 0xff)
	{
		return a;
	}
	if (fb.exponent == 0xff)
	{
		return b;
	}

	if (fa.exponent > fb.exponent)
	{
		fa.val = b;
		fb.val = a;
	}

	uint32_t sig_a, sig_b, sig_res;
	sig_a = fa.fraction;
	if (fa.exponent != 0)
		sig_a |= 0x800000; // the hidden 1
	sig_b = fb.fraction;
	if (fb.exponent != 0)
		sig_b |= 0x800000; // the hidden 1

	// alignment shift for fa
	uint32_t shift = 0;

	/* TODO: shift = ? */
    shift = fb.exponent-fa.exponent;
    if(((sig_a>>23)&1)==0&&((sig_b>>23)&1)==1)
    {
        --shift;
    }
	sig_a = (sig_a << 3); // guard, round, sticky
	sig_b = (sig_b << 3);

	uint32_t sticky = 0;
	while (shift > 0)
	{
		sticky = sticky | (sig_a & 0x1);
		sig_a = sig_a >> 1;
		sig_a |= sticky;
		shift--;
	}

	// fraction add
	if (fa.sign)
	{
		sig_a *= -1;
	}
	if (fb.sign)
	{
		sig_b *= -1;
	}

	sig_res = sig_a + sig_b;

	if (sign(sig_res))
	{
		f.sign = 1;
		sig_res *= -1;
	}
	else
	{
		f.sign = 0;
	}

	uint32_t exp_res = fb.exponent;
	//printf("fa=%f fb=%f exp_res=%d,sig_res=%d  ",fa.fval,fb.fval,exp_res, sig_res);
	uint32_t ans = internal_normalize(f.sign, exp_res, sig_res);
	//FLOAT ans_t;
	//ans_t.val=ans;
	//printf(" ans-t.val=%d ans-t.val=%f ans=%f  t.val=%f",ans-t.val,ans_t.fval-t.fval,ans_t.fval,t.fval);
	//printf(" ans.exponent=%d t.exponent=%d ans.fraction=%d t.fraction=%d\n\n",ans_t.exponent,t.exponent,ans_t.fraction,t.fraction);
	return ans;
}

CORNER_CASE_RULE corner_sub[] = {
	{P_ZERO_F, P_ZERO_F, P_ZERO_F},
	{N_ZERO_F, P_ZERO_F, N_ZERO_F},
	{P_ZERO_F, N_ZERO_F, P_ZERO_F},
	{N_ZERO_F, N_ZERO_F, P_ZERO_F},
	{P_NAN_F, P_NAN_F, P_NAN_F},
	{N_NAN_F, P_NAN_F, P_NAN_F},
	{P_NAN_F, N_NAN_F, P_NAN_F},
	{N_NAN_F, N_NAN_F, N_NAN_F},
};

// a - b
uint32_t internal_float_sub(uint32_t b, uint32_t a)
{
	// change the sign of b
	int i = 0;
	for (; i < sizeof(corner_sub) / sizeof(CORNER_CASE_RULE); i++)
	{
		if (a == corner_sub[i].a && b == corner_sub[i].b)
			return corner_sub[i].res;
	}
	if (a == P_NAN_F || a == N_NAN_F)
		return a;
	if (b == P_NAN_F || b == N_NAN_F)
		return b;
	FLOAT fb;
	fb.val = b;
	fb.sign = ~fb.sign;
	return internal_float_add(fb.val, a);
}

CORNER_CASE_RULE corner_mul[] = {
	{P_ZERO_F, P_INF_F, N_NAN_F},
	{P_ZERO_F, N_INF_F, N_NAN_F},
	{N_ZERO_F, P_INF_F, N_NAN_F},
	{N_ZERO_F, N_INF_F, N_NAN_F},
	{P_INF_F, P_ZERO_F, N_NAN_F},
	{P_INF_F, N_ZERO_F, N_NAN_F},
	{N_INF_F, P_ZERO_F, N_NAN_F},
	{N_INF_F, N_ZERO_F, N_NAN_F},
};

// a * b
uint32_t internal_float_mul(uint32_t b, uint32_t a)
{
	int i = 0;
	for (; i < sizeof(corner_mul) / sizeof(CORNER_CASE_RULE); i++)
	{
		if (a == corner_mul[i].a && b == corner_mul[i].b)
			return corner_mul[i].res;
	}

	if (a == P_NAN_F || a == N_NAN_F || b == P_NAN_F || b == N_NAN_F)
		return a == P_NAN_F || b == P_NAN_F ? P_NAN_F : N_NAN_F;

	FLOAT fa, fb, f;
	fa.val = a;
	fb.val = b;
	f.sign = fa.sign ^ fb.sign;
    //test 
    //FLOAT t;
    //t.fval = fa.fval * fb.fval;
    
	if (a == P_ZERO_F || a == N_ZERO_F)
		return fa.sign ^ fb.sign ? N_ZERO_F : P_ZERO_F;
	if (b == P_ZERO_F || b == N_ZERO_F)
		return fa.sign ^ fb.sign ? N_ZERO_F : P_ZERO_F;
	if (a == P_INF_F || a == N_INF_F)
		return fa.sign ^ fb.sign ? N_INF_F : P_INF_F;
	if (b == P_INF_F || b == N_INF_F)
		return fa.sign ^ fb.sign ? N_INF_F : P_INF_F;

	uint64_t sig_a, sig_b, sig_res;
	sig_a = fa.fraction;
	if (fa.exponent != 0)
		sig_a |= 0x800000; // the hidden 1
	sig_b = fb.fraction;
	if (fb.exponent != 0)
		sig_b |= 0x800000; // the hidden 1

	if (fa.exponent == 0)
		fa.exponent++;
	if (fb.exponent == 0)
		fb.exponent++;

	sig_res = sig_a * sig_b; // 24b * 24b
	uint32_t exp_res = 0;

	/* TODO: exp_res = ? leave space for GRS bits. */
    exp_res=fa.exponent+fb.exponent - 20 - 127;
	//printf("fa=%f fb=%f fa.exp=%d fb.exp=%d exp_res=%d",fa.fval,fb.fval,fa.exponent,fb.exponent,exp_res);
	uint32_t ans = internal_normalize(f.sign, exp_res, sig_res);
	//FLOAT ans_t;
	//ans_t.val=ans;
	//printf(" ans-t.val=%d ans-t.val=%f ans=%f  t.val=%f",ans-t.val,ans_t.fval-t.fval,ans_t.fval,t.fval);
	//printf(" ans.exponent=%d t.exponent=%d ans.fraction=%d t.fraction=%d\n\n",ans_t.exponent,t.exponent,ans_t.fraction,t.fraction);
	return ans;
}

CORNER_CASE_RULE corner_div[] = {
	{P_ZERO_F, P_ZERO_F, N_NAN_F},
	{N_ZERO_F, P_ZERO_F, N_NAN_F},
	{P_ZERO_F, N_ZERO_F, N_NAN_F},
	{N_ZERO_F, N_ZERO_F, N_NAN_F},
	{P_INF_F, P_ZERO_F, P_INF_F},
	{N_INF_F, P_ZERO_F, N_INF_F},
	{P_INF_F, N_ZERO_F, N_INF_F},
	{N_INF_F, N_ZERO_F, P_INF_F},
	{P_INF_F, P_INF_F, N_NAN_F},
	{N_INF_F, P_INF_F, N_NAN_F},
	{P_INF_F, N_INF_F, N_NAN_F},
	{N_INF_F, N_INF_F, N_NAN_F},
};
// a / b
uint32_t internal_float_div(uint32_t b, uint32_t a)
{

	int i = 0;
	for (; i < sizeof(corner_div) / sizeof(CORNER_CASE_RULE); i++)
	{
		if (a == corner_div[i].a && b == corner_div[i].b)
			return corner_div[i].res;
	}

	FLOAT f, fa, fb;
	fa.val = a;
	fb.val = b;

	if (a == P_NAN_F || a == N_NAN_F || b == P_NAN_F || b == N_NAN_F)
		return a == P_NAN_F || b == P_NAN_F ? P_NAN_F : N_NAN_F;
	if (a == P_INF_F || a == N_INF_F)
	{
		return fa.sign ^ fb.sign ? N_INF_F : P_INF_F;
	}
	if (b == P_ZERO_F || b == N_ZERO_F)
	{
		return fa.sign ^ fb.sign ? N_INF_F : P_INF_F;
	}
	if (a == P_ZERO_F || a == N_ZERO_F)
	{
		fa.sign = fa.sign ^ fb.sign;
		return fa.val;
	}
	if (b == P_INF_F || b == N_INF_F)
	{
		return fa.sign ^ fb.sign ? N_ZERO_F : P_ZERO_F;
	}

	f.sign = fa.sign ^ fb.sign;

	uint64_t sig_a, sig_b, sig_res;
	sig_a = fa.fraction;
	if (fa.exponent != 0)
		sig_a |= 0x800000; // the hidden 1
	sig_b = fb.fraction;
	if (fb.exponent != 0)
		sig_b |= 0x800000; // the hidden 1

	// efforts to maintain the precision of the result
	int shift = 0;
	while (sig_a >> 63 == 0)
	{
		sig_a <<= 1;
		shift++;
	}
	while ((sig_b & 0x1) == 0)
	{
		sig_b >>= 1;
		shift++;
	}

	sig_res = sig_a / sig_b;

	if (fa.exponent == 0)
		fa.exponent++;
	if (fb.exponent == 0)
		fb.exponent++;
	uint32_t exp_res = fa.exponent - fb.exponent + 127 - (shift - 23 - 3);
	return internal_normalize(f.sign, exp_res, sig_res);
}

void fpu_load(uint32_t val)
{
	fpu.status.top = fpu.status.top == 0 ? 7 : fpu.status.top - 1;
	fpu.regStack[fpu.status.top].val = val;
}

uint32_t fpu_store()
{
	uint32_t val = fpu.regStack[fpu.status.top].val;
	fpu.status.top = (fpu.status.top + 1) % 8;
	return val;
}

uint32_t fpu_peek()
{
	uint32_t val = fpu.regStack[fpu.status.top].val;
	return val;
}

void fpu_add(uint32_t val)
{
	/*
	float *a = (float*)&fpu.regStack[fpu.status.top].val;
	float *b = (float*)&val;
	float c = *a + *b;
	uint32_t *d = (uint32_t *)&c;
	fpu.regStack[fpu.status.top].val = *d;
	*/
	fpu.regStack[fpu.status.top].val = internal_float_add(val, fpu.regStack[fpu.status.top].val);
}

void fpu_add_idx(uint32_t idx, uint32_t store_idx)
{
	/*
	float *a = (float*)&fpu.regStack[fpu.status.top].val;
	float *b = (float*)&fpu.regStack[(fpu.status.top + idx) % 8].val;
	float c = *a + *b;
	uint32_t *d = (uint32_t *)&c;
	fpu.regStack[(fpu.status.top + store_idx) % 8].val = *d;
	*/
	uint32_t a = fpu.regStack[fpu.status.top].val;
	uint32_t b = fpu.regStack[(fpu.status.top + idx) % 8].val;
	fpu.regStack[(fpu.status.top + store_idx) % 8].val = internal_float_add(b, a);
}

void fpu_sub(uint32_t val)
{
	/*
	float *a = (float*)&fpu.regStack[fpu.status.top].val;
	float *b = (float*)&val;
	float c = *a - *b;
	//printf("f %f - %f = %f\n", *a, *b, c);
	uint32_t *d = (uint32_t *)&c;
	fpu.regStack[fpu.status.top].val = *d;
	*/
	fpu.regStack[fpu.status.top].val = internal_float_sub(val, fpu.regStack[fpu.status.top].val);
}

void fpu_mul(uint32_t val)
{
	/*
	float *a = (float*)&fpu.regStack[fpu.status.top].val;
	float *b = (float*)&val;
	float c = *a * *b;
	uint32_t *d = (uint32_t *)&c;
	fpu.regStack[fpu.status.top].val = *d;
	*/
	fpu.regStack[fpu.status.top].val = internal_float_mul(val, fpu.regStack[fpu.status.top].val);
}

void fpu_mul_idx(uint32_t idx, uint32_t store_idx)
{
	/*
	float *a = (float*)&fpu.regStack[fpu.status.top].val;
	float *b = (float*)&fpu.regStack[(fpu.status.top + idx) % 8].val;
	float c = *a * *b;
	uint32_t *d = (uint32_t *)&c;
	fpu.regStack[(fpu.status.top + store_idx) % 8].val = *d;
	*/
	uint32_t a = fpu.regStack[fpu.status.top].val;
	uint32_t b = fpu.regStack[(fpu.status.top + idx) % 8].val;
	fpu.regStack[(fpu.status.top + store_idx) % 8].val = internal_float_mul(b, a);
}

void fpu_div(uint32_t val)
{
	/*
	float *a = (float*)&fpu.regStack[fpu.status.top].val;
	float *b = (float*)&val;
	float c = *a / *b;
	// printf("f %f / %f = %f\n", *a, *b, c);
	uint32_t *d = (uint32_t *)&c;
	fpu.regStack[fpu.status.top].val = *d;
	*/
	fpu.regStack[fpu.status.top].val = internal_float_div(val, fpu.regStack[fpu.status.top].val);
}

void fpu_xch(uint32_t idx)
{
	idx = (fpu.status.top + idx) % 8;
	uint32_t temp = fpu.regStack[fpu.status.top].val;
	fpu.regStack[fpu.status.top].val = fpu.regStack[idx].val;
	fpu.regStack[idx].val = temp;
}

void fpu_copy(uint32_t idx)
{
	idx = (fpu.status.top + idx) % 8;
	fpu.regStack[idx].val = fpu.regStack[fpu.status.top].val;
}

void fpu_cmp(uint32_t idx)
{
	idx = (fpu.status.top + idx) % 8;
	float *a = (float *)&fpu.regStack[fpu.status.top].val;
	float *b = (float *)&fpu.regStack[idx].val;
	if (*a > *b)
	{
		fpu.status.c0 = fpu.status.c2 = fpu.status.c3 = 0;
		//printf("f %f > %f\n", *a, *b);
		//printf("f %x > %x\n", *((uint32_t *)a), *((uint32_t *)b));
	}
	else if (*a < *b)
	{
		fpu.status.c0 = 1;
		fpu.status.c2 = fpu.status.c3 = 0;
		//printf("f %f < %f\n", *a, *b);
	}
	else
	{
		fpu.status.c0 = fpu.status.c2 = 0;
		fpu.status.c3 = 1;
		//printf("f %f == %f\n", *a, *b);
	}
}

void fpu_cmpi(uint32_t idx)
{
	idx = (fpu.status.top + idx) % 8;
	float *a = (float *)&fpu.regStack[fpu.status.top].val;
	float *b = (float *)&fpu.regStack[idx].val;
	if (*a > *b)
	{
		cpu.eflags.ZF = cpu.eflags.PF = cpu.eflags.CF = 0;
	}
	else if (*a < *b)
	{
		cpu.eflags.ZF = cpu.eflags.PF = 0;
		cpu.eflags.CF = 1;
	}
	else
	{
		cpu.eflags.CF = cpu.eflags.PF = 0;
		cpu.eflags.ZF = 1;
	}
}

```

