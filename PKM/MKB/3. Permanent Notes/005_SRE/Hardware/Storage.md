

### Channel
- Channel is data pipeline between CPU and RAM on motherboard
- single channel vs dual channel vs quad channel ( one way or two way street )
**- Try to use dual channel mode where available by placing at least two DIMMs placed in slot that correspond to separate channels A and B**

### DIMM
- Is a stick of RAM which consists of PCB on which memory modules are arranged and in some whole thing is covered with heat spreader on a gaming RAM.
- Each DIMM are socketed into DIMM slots on motherboard
- For budget server, you can use motherboard with two DIMM slots i.e with two DIMM for each channel
**- Try to look for at least 4 DIMM slots and two DIMMs for each channel.**
- Generally labeled left to right A1, A2, B1, B2
- If you want to use your RAM in dual channel mode, you must populate at lest one A slot and one B slot ( so for two DIMM use A1, B1 or A2, B2)

### Rank
- single rank vs dual rank DIMM
- using single rank DIMM in motherboard will result in PC treating same as two dual rank DIMMs
- Basically tells how many sides of DIMM are populated with memory modules, either single for one side or dual for both sides

### RAM
- Memory modules are produced by larger OEM: samsung, micron, sk
- Check for voltage tolerance
- Better RAM will reach higher clock speeds with lower voltage
- Check using B die finder.

### binning
- testing memory module for voltage tolerance and pricing them

### What to look for
- Capacity: 
	- 16 to 32 G for two DIMM kit
- Speed
	- Measured in MT/s ( mega transfers per second )
	- How quickly RAM can send and receive data
	- DDR4 starts as 2133 MT/s
	- Common 3200 MT/2 and 3600 MT/s
- CAS
	- Column Address Strobe
	- Written in CLXX where XX is the number of clock cycles that CPI memory controller takes to receive data after sending a request.
	- Lower the number better 
	- DDR4 goes from CL12 to CL22
	- Most common CAS latencies CL14 and CL18

### FWL (First word latency)
***- Amount of time it takes for RAM to respond to request from CPU in nanoseconds***
- FWL = CAS latency / (0.5 * Speed*)
- 3200 CL16 vs 3600 CL18 = pick the cheapest

### Overclocking
- XMP ( eXtreme memory profile) or DOCP (Direct overclocking profile) on AMD
***- We basically try to overclock RAM ourself by using pre-loaded profile***
- XMP/DOCP is a pretty idiot proof and any modern processor should have a strong enough memory controller to handle up to four DIMMs at almost any serttings.
***- If you do not set XMP / DOCP , RAM will remain at the base spec of your CPU and you give up free performance so don't forget.***
'

### What to buy
- For office PC: single 8 GB stick of RAM with whatever timeings and speeds you can find.
- For gaming: at least 2 stick to get dual channel mode, also two 8 GB DIMMs for 16 Gb total, either 3200 CL16 or 3600 CL38
- For production workload ( servers ): 32 GB RAM with better timings like 3200 CL14 or 3600 CL16. 4 8GB DIMM will guaranteee the performance benefit fo dual rank, or or you can od two 16G DIMM