# International Data Encryption Algorithm(IDEA)
IDEA is one of the encryption algorithms that is widely used for security purpose. IDEA block cipher operates with 64-bit plain text block and 64-bit cipher text block, and a 128-bit key controls it. The fundamental design of the algorithm is using three different algebraic operations: bitwise Exclusive OR, multiplication modulo, and addition modulo. Having the largest number of weak keys is one of the drawbacks of IDEA  
![xyz](https://user-images.githubusercontent.com/63008519/78742344-5eff1300-7979-11ea-8838-223d326280e2.png)
# Description
IDEA uses 52 subkeys, each 16 bits long. Two are used during each round proper, and four are used before every round and after the last round. It has eight rounds.    
The plaintext block in IDEA is divided into four quarters, each 16 bits long. Three operations are used in IDEA to combine two 16 bit values to produce a 16 bit result, addition, XOR, and multiplication. Addition is normal addition with carries, modulo 65,536. Multiplication, as used in IDEA, requires some explanation.    
Multiplication by zero always produces zero, and is not invertible. Multiplication modulo n is also not invertible whenever it is by a number which is not relatively prime to n. The way multiplication is used in IDEA, it is necessary that it be always invertible. This is true of multiplication IDEA style.  
The number 65,537, which is 2^16+1, is a prime number. (Incidentally, 2^8+1, or 257, is also prime, and so is 2^4+1, or 17, but 2^32+1 is not prime, so IDEA cannot be trivially scaled up to a 128-bit block size.) Thus, if one forms a multiplication table for the numbers from 1 through 65,536, each row and column will contain every number once only, forming a Latin square, and providing an invertible operation.  
The numbers that 16 bits normally represent are from 0 to 65,535 (or, perhaps even more commonly, from -32,768 to 32,767). In IDEA, for purposes of multiplication, a 16 bit word containing all zeroes is considered to represent the number 65,536; other numbers are represented in conventional unsigned notation, and multiplication is modulo the prime number 65,537.
# Encryption in Idea
Let the four quarters of the plaintext be called A, B, C, and D, and the 52 subkeys called K(1) through K(52).
Before round 1, or as the first part of it, the following is done:
Multiply A by K(1). Add K(2) to B. Add K(3) to C. Multiply D by K(4).  
Round 1 proper consists of the following:  
• Calculate A xor C (call it E) and B xor D (call it F).  
• Multiply E by K(5). Add the new value of E to F.  
• Multiply the new value of F by K(6). Add the result, which is also the new value of F, to E.  
• Change both A and C by XORing the current value of F with each of them; change both B and D by XORing the current value of E with each of them.  
• Swap B and C.  
• Repeat all of this eight times, or seven more times, using K(7) through K(12) the second time, up to K(43) through K(48) the eighth time. Note that the swap of B and C is not performed after round 8.  
Then multiply A by K(49). Add K(50) to B. Add K(51) to C. Multiply D by K(52).
# Decryption in Idea
How can the round in IDEA be reversed, since all four quarters of the block are changed at the same time, based on a function of all four of their old values? Well, the trick to that is that A xor C isn't changed when both A and C are XORed by the same value, that value cancels out, no matter what that value might be. And the same applies to B xor D. And since the values used are functions of (A xor C) and (B xor D), they are still available.    
Those that are added are replaced by their two's complement. Those that are multiplied in are replaced by their multiplicative inverse, modulo 65,537, in IDEA notation when used to change blocks directly, but those used to calculate the cross-footed F-functions are not changed. Keys XORed in would not need to be changed, but there aren't any such keys in IDEA. Due to the placement of the swap, the first four keys for decryption are moved somewhat differently than the other keys used for the same operation between rounds.  
The decryption key schedule is:  
The first four subkeys for decryption are:    
KD(1) = 1/K(49)  
KD(2) =  -K(50)  
KD(3) =  -K(51)  
KD(4) = 1/K(52)  
and they do not quite follow the same pattern as the remaining subkeys which follow.  
The following is repeated eight times, adding 6 to every decryption key's index and subtracting 6 from every encryption key's index:  
KD(5)  =   K(47)  
KD(6)  =   K(48)  
KD(7)  = 1/K(43)  
KD(8)  =  -K(45)  
KD(9)  =  -K(44)  
KD(10) = 1/K(46)  
# Subkey generation
The 128-bit key of IDEA is taken as the first eight subkeys, K(1) through K(8). The next eight subkeys are obtained the same way, after a 25-bit circular left shift, and this is repeated until all encryption subkeys are derived.
This method of subkey generation is regular, and this may be a weakness. However, IDEA is considered to be highly secure, having stood up to all forms of attack so far tried by the academic community.
# Algorithm
Four blocks of cleartext each 16 bit wide are encrypted at a time. The IDEA-algorithm consists of 8 steps of encryption (also called rounds) with a final output transformation. The necessary partial keys for each cycle and the final transformation are generated from a 128 bit long input key. For each round  the following modules are required: 
• bitwise XOR module for 16 bit blocks 
• Addition of 16 bit numbers modulo 2^16
• Multiplication of 16 bit numbers modulo 2^16 + 1, where the input 0 is substituted by 2^16
In the output transformation  we need the following modules: 
• Adding 16 bit numbers modulo 2^16 
• Multiplying 16 bit numbers modulo 2^16 + 1, where the input 0 is substituted by 2^16.
 The key generator uses a 128 bit input word to generate 52 partial keys of 16 bit each. For the encryption the 128 bit input is split into 8 blocks of 16 bit each, which are used as the 8 first partial keys .
![123](https://user-images.githubusercontent.com/63008519/78744412-2104ed80-797f-11ea-8e03-46812670e3d5.png)
# Xilinx procedure
# Xor module
The IDEA algorithm uses 48 XOR modules(6 FOR EACH ROUND) for a 16-bit wide word (input and output). Create a XOR module with two input vectors and one output vector of the vector type for 16 bit width and a suitable testbench by following these steps:
• Create a module EXOR (Verilog Module) in the editor of the IDEA 1 project
 • Describe the input and output of the EXOR module and assign the operation  
• After saving the file, switch the view to Simulation in the left upper corner and start the process Behavioral Check Syntax on the left side. Correct any mistakes and check the syntax again until no further errors are displayed. After creating the XOR module, we need to implement a testbench which can apply different input patterns to the module. By comparing the output values of the XOR module to the expected values we can confirm the functional correctness function of the module.
The testbench is implemented as follows:
• Create a module exor_tb(Verilog Test Bench).  Now apply various test signals to the inputs of the module . Please note that we do not have any clock in the direct implementation. After the syntax errors have been checked, start the  Simulator . 
# Adder module
Next we need 34 modules for 16-bit addition modulo 2^16 for the IDEA algorithm . The task is to create an adder module providing these features.Here we use ripple carry adder using full adder for faster operation.
• Create a module  Adder(Verilog Module) in the editor of the IDEA 1 project
• Describe the input and output of the adder module and assign the operation  
• After saving the file, switch the view to Simulation in the left upper corner and start the process Behavioral Check Syntax on the left side. Correct any mistakes and check the syntax again until no further errors are displayed.
# Modulo-multiplier
Used 34 times, the modulo multiplier is the most complex module in the IDEA algorithm. Two 16-bit input vectors are multiplied, where the input of 0 is substituted by 2^16. The 33 bit (33 bits needed because of 2^16) wide result is taken modulo 2^16 + 1 and cut down to use only the lowest 16 bit as output.
A separate implementation of the multiplication and the modulo operation would lead to two very complex and therefore expensive modules. In addition, these would have to be connected with a 33 bit wide datapath. A better solution is the low-high algorithm. Based on a single, modified multiplier the multiplication and the modulo operation can be performed in one module.
