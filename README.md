# Microsoft-Sculpt-BLE-ZMK

This project was born out of boredom as most personal projects are. I had a Microsoft Sculpt Keyboard remaining around when I was dealing with RSI from coding all day. I tried this first because I was able to find one for a steal, unfortunately as many have found - the proprietary wireless connection is HOT TRASH. I was still getting dropped keys with 6 inches of clear line of sight between the dongle and keyboard. Since then I switched to a Logitech K860 and it has been significantly better in terms of connection and overall usability.

Upon disassembly you find a 1mm pitched 30 pin ribbon cable in this format:
| 1| 2| 3|  ...#  |28|29|30|
|  |  |  |     |  |  |  ∟--|     30
|  |  |  |     |  |  ∟-----|     29  
|  |  |  |     |  ∟--------|     28
|  |  |  |     ∟-----------| ... #
|  |  |  ∟-----------------|     3  
|  ∟-----------------------|     2  
∟--------------------------|     1  

The following is based on the hardwork done by [Chris Paynter](https://chrispaynter.medium.com/modding-the-microsoft-sculpt-ergonomic-keyboard-to-run-qmk-41d3d1caa7e6) and [blttll](https://github.com/blttll/tmk_keyboard/blob/master/keyboard/sculpt/README.md) who did the hardwork of mapping the various keys to their corresponding pinds.
## Here are the pinouts to describe what the matrix needs to be:
Pinout layout:
|Col/Row|0          |1          |2          |3          |4          |5          |6          |7          |8          |9          |10         |11         |12       |13         |14       |15        |16     |17     |
|--     |:--:       |:--:       |:--:       |:--:       |:--:       |:--:       |:--:       |:--:       |:--:       |:--:       |:--:       |:--:       |:--:     |:--:       |:--:     |:--:      |:--:   |--:    |
|	0     |	          |	PAUS      |	          |	DEL       |	0         |	9         |	          |	8         |	BSPC      |	7         |	TAB       |	Q         |	2       |	1         |	        |	         |	     |    	 |
|	1     |	          |	PGUP      |	          |	F12       |	LBRC      |	MINS      |	          |	RBRC      |	INS       |	Y         |	F5        |	F3        |	W       |	4         |	        |	F6       |       |       |
|	2     |	          |	HOME      |	          |	CALC      |	P         |	O         |	          |	I         |	          |	U         |	R         |	E         |	CAPS    |	3         |	        |	T        |	     |	     |
|	3     |	          |	SLCK      |	          |	ENT       |	SCLN      |	L         |	          |	K         |	BSLS      |	J         |	F         |	D         |	(NUBS)  |	A         |	        |	LGUI     |	     |	     |
|	4     |	          |	          |	          |	APP       |	SLSH      |	QUOT      |	RALT      |	          |	LEFT      |	H         |	G         |	F4        |	S       |	ESC       |	        |	         | LALT  |       |
|	5     |	          |	END       |	RSFT      |	PGDN      |	          |	DOT       |	          |	COMM      |	          |	M         |	V         |	C         |	X       |	Z         |	LSFT    |	         |	     |	     |
|	6     |	LCTL      |	RGHT      |	          |	UP        |	DOWN      |	          |	          |	          |	RSPC      |	N         |	B         |	LSPC      |	        |	          |	        |	         |       | RCTL  |
|	7     |	          |	PSCR      |	          |	F11       |	EQL       |	F9        |	          |	F8        |	F10       |	F7        |	5         |	F2        |	F1      |	GRV       |	        |	6        |	     |	     |

We only have 21 GPIO pins on the nRF52840 Pro Micro Board, so we need to consolidate the values if possible - in this case we're in luck. We have a couple of columns with single keys (0,2,6,14,16,17) and corresponding empty slots in various other columns that won't override the existing mapping.

## Post consolidation:
|Ribbon Cable Pin # |             |11+16				|13				|14+12		|15+10		|17				|18+24				|19				|20				|21				|22				|23			|25+26+27		|
|--							    |:--:					|:--:					|:--:			|:--:			|:--:			|:--:			|:--:					|:--:		  |:--:			|:--:			|:--:			|:--:		|--:				|
|							     	|Col/Row			|1+6					|3				|4+2			|5+0			|7				|8+14					|9				|10				|11				|12				|13			|15+16+17		|
|	2						    	|	0						|	PAUS				|	DEL			|	0				|	9				|	8				|	BSPC				|	7				|	TAB			|	Q				|	2				|	1			|						|
|	3					  	  	|	1						|	PGUP				|	F12			|	LBRC		|	MINS		|	RBRC		|	INS					|	Y				|	F5			|	F3			|	W				|	4			|	F6				|
|	4						    	|	2						|	HOME				|	CALC		|	P				|	O				|	I				|							|	U				|	R				|	E				|	CAPS		|	3			|	T					|
|	5						    	|	3						|	SLCK				|	ENT			|	SCLN		|	L				|	K				|	BSLS				|	J				|	F				|	D				|	(NUBS)	|	A			|	LGUI			|
|	6						    	|	4						|	LALT (6)		|	APP			|	SLSH		|	QUOT		|					|	LEFT				|	H				|	G				|	F4			|	S				|	ESC		|	LALT(16)	|
|	7						    	|	5						|	END					|	PGDN		|	RSFT (2)|	DOT			|	COMM		|	LSFT (14)		|	M				|	V				|	C				|	X				|	Z			|						|
|	8				    			|	6						|	RGHT				|	UP			|	DOWN		|	RCTL (0)|					|	RSPC				|	N				|	B				|	LSPC		|					|				|	RCTL(17)	|
|	9					    		|	7						|	PSCR				|	F11			|	EQL			|	F9			|	F8			|	F10					|	F7			|	5				|	F2			|	F1			|	GRV		|	6					|
