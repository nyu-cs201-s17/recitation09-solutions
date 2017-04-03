Recitation 9 : Solutions
=================

## Problem 1

Answers to the parts in problem 1 are:

- The variable result must be in %rax, since this value gets returned as the final value. Parameter x is passed in %rdi. Parameter n is passed in %esi and then copied into %ecs. Register %edx is initialized to 1. We can infer that mast must be %rdx.

- They are initialized to 0 and 1 respectively.

- The condition for continuing the loop is that mask is nonzero since we use test along with jne.

- The salq instruction updates mask to be mask << n.

- Variable result is updated to be result | (x&mask).

The C code is as follows:

```
	long loop(long x, int n)
	{
		long result = 0;
		long mask;
		for (mask = 0x1; mask != 0 ; mask = mask << n) {
			result |= (x & mask);
		}
		return result;
	}
``` 

## Problem 2

Let us first examine the code with some annotations:

```
	# void test(long i, b_struct *bp)
	# i in %rdi, bp in %rsi

	0000000000000000 <test>:
	  0:  8b 8e 20 01 00 00 	mov 	0x120(%rsi), %ecx		## Get bp->last
	  6:  03 0e			add 	(%rsi), %ecx			## Add bp->first
	  8:  48 8d 04 bf 		lea 	(%rdi, %rdi, 4), %rax
	  c:  48 8d 04 c6 		lea 	(%rsi, %rax, 8), %rax
	 10:  48 8b 50 08 		mov 	0x8(%rax), %rdx			## Get ap->idx
	 14:  48 63 c9 			movslq	%ecx, %rcx			## Sign extend n
	 17:  48 89 4c d0 10 		mov 	%rcx, 0x10(%rax, %rdx, 8)	## Store n

```
Observe that the mov instruct on 10: performs an 8 byte read to dereference ap->idx. So the element idx in a_struct is a long and therefore the struct has an 8 byte alignment requirement.

Let us say that a struct is A bytes, and CNT is C. The code to retrieve bp->last references offset 0x120, or 288. We can therefore see that the layout of b struct must consist of 4 bytes for element first, 4 bytes of padding, 280 bytes for array of a, 4 bytes for last, and 4 bytes of additional padding. We can therefore conclude that C · A = 280.

Let us say that pointer bp has value p. Then in retrieving ap->idx (line 6), the program references address p + 8 + 40i, implying that A = 40,and therefore C = 7.

Answers to the parts in problem 2 are:

- CNT = 7

```
	typedef struct {
		long idx;
		long x[4];
	} a_struct;
```