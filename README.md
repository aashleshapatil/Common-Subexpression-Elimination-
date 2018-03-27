# Common-Subexpression-Elimination-

Objectives
•	Implement an LLVM pass that performs Common Subexpression Elimination.
•	Leverage LLVM to perform simple instruction simplification.
•	Perform a simple load-store optimization during the CSE traversal to find additional redundancy while preserving the order of memory operations.

Specification
In this project, you will implement Common Subexpression Elimination plus a few other simple optimizations.  I’ll describe each of them below.

•	Common Subexpression Elimination

o	For each instruction, eliminate all other instructions which are literal matches:
			Same opcode
			Same type (LLVMTypeOf of the instruction not its operands)
			Same number of operands
			Same operands in the same order (no commutativity)
o	Do not consider Loads, Stores, Terminators, VAArg, Calls, Allocas, and FCmps for elimination.
o	To avoid implementation complexity, I recommend using a nested loop (it may actually involve recursion on the dominator tree)
o	Create a counter of all instructions you eliminate here and call it CSE_Basic.  You will dump this to standard output at the end of your pass.
	
	•	Optimization 0: Eliminate dead instructions
		o	While you visit each instruction, check if it is dead.
		o	If so, remove the instruction.
		o	Count these simplifications as CSE_Dead.
		
	•	Optimization 1: Simplify Instructions
		o	While you visit each instruction, check if it can be simplified through simple constant folding.
		o	Use the InstructionSimplify function that I’ve provided in transform.h.  For C++, call the SimplifyInstruction function directly.
		o	Count these simplifications as CSE_Simplify.
		
	•	Optimization 2: Eliminate Redundant Loads
		o	While traversing the instructions in a single basic block, if you come across a load we will look to see if there are redundant loads within the same basic block only.
		o	You may only eliminate a later load as redundant if the later load is not volatile, it loads the same address, it loads the same type operand, and there are no intervening stores or calls to any address.

	•	Optimization 3: Eliminate Redundant Stores (and Loads)
		o	If you find two stores to the same address with no intervening loads and the earlier store is not volatile, you should remove the earlier one.
				These are simply back-to-back stores and the earlier one is redundant with the later one.
		o	When you find a store, look to see if there is a non-volatile load to the same address after the store within the same basic block.
				If such a case occurs, replace all uses of the load with the store’s data operand.

		•	You should treat call instructions as stores to an unknown and possibly the same address.
		•	At the end of your pass, print a total of all instructions removed and show the breakdown across each category.  You may print this directly to standard output, but please format the data in an easy to read manner.
		•	You may enhance my approach as you see fit.  But, do not change the meaning of the counted values.  If you can remove more loads/stores, then add a separate counter to track that.
   ******** The major code for CSE is written in C language in file CSE_C.c********
