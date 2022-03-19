# คู่มือสำหรับการเขียน Custom Script

## DEFINATION

`Custom Script` ในที่นี้ คือไฟล์ Script ตรวจประเภทหนึ่งที่จะใช้พิจารณาว่า ผล Output จากผู้ส่งมา **ผ่านหรือไม่ผ่าน (P หรือ -)** ไม่สามารถกำหนดคะแนนได้

## Custom Script

คือเป็นไฟล์ Script **พิเศษ**ที่จะสามารถใช้ในการพิจารณาว่า ผล Output จากผู้ส่งมา ผ่านหรือไม่ผ่าน `(P หรือ -)` โดยปกติจะใช้สำหรับโจทย์ที่สามารถมี Output ได้หลายแบบ เช่น ข้อ [706 อะไรก็ได้ อย่าเรียงก็พอ](https://api.otog.cf/problem/doc/706) ที่คำตอบสามารถเป็นอะไรก็ได้ ที่…อย่าเรียงก็พอ5555 หรือพวกข้อที่มีการใช้คำต้องห้าม `(เช่น ห้ามมี for while do)` หรือข้อที่เขียนได้เฉพาะภาษา C `(ส่งภาษาอื่นได้ -)`
ในการสร้าง `Custom script` สามารถทำได้โดยการตั้งชื่อไฟล์และภาษา ตามที่กำหนด จากนั้นให้เอาไฟล์นั้น ๆ ใส่เข้าไปในกลุ่มไฟล์เทสเคส เพียงแค่นั้น **ก็จะได้โจทย์ที่สามารถใช้ CUSTOM SCRIPT ได้แล้ว!!!!!**
ในปัจจุบัน `Custom Script` จะมี 2 ลักษณะ นั้น `check.cpp` และ `interactive_script.py`

## check.cpp

เป็นไฟล์ Script ที่มีมาตั้งแต่สมัยก่อน ที่พี่กฤษฏิ์เขียนไว้ _(BLESSED)_
โดยข้อมูลที่ `check.cpp` จะรู้คือ

1. ที่อยู่ของไฟล์ `.sol` ของเคสแต่ละเคส
2. ที่อยู่ของไฟล์ `.in` ของเคสแต่ละเคสเช่นกัน
3. ที่อยู่ของไฟล์โค้ดของที่ส่ง

- นอกจากนี้ ตัวตรวจสามารถรับ Output ของผู้ส่งได้จากการอ่านไฟล์ `output.txt` ได้เลย
- ผลการตรวจสามารถบอกได้จากการเขียนไฟล์ `grader_result.txt` ว่าผ่านหรือไม่ผ่าน
- โดยผ่านจะใช้สัญลักษณ์ `P` และไม่ผ่านจะใช้สัญลักษณ์ ขีด หรือ `W`

### ตัวอย่างโจทย์

แมวของคุณชอบเลข 69 หรือต่ำกว่า หากเกินนั้น แมวของคุณจะไม่สนใจทันที

งานของคุณคือ ให้เขียนโปรแกรมแสดงตัวเลข 69 หรือต่ำกว่า บนหน้าจอ

#### Input

(ไม่มี)

#### Output

จำนวนเต็ม หนึ่งจำนวนที่น้อยกว่าหรือเท่ากับ 69

#### ตัวอย่าง check.cpp ของข้อนี้

```cpp
#include <iostream>
#include <string>
#include <fstream>
#include <cstdlib>
#include <algorithm>
using namespace std;

std::ofstream result("grader_result.txt"); // ตัวเปิดไฟล์ "grader_result.txt"
void correct() {
	::result << 'P'; // เขียนไฟล์นั้นว่า "P"
	::exit(0);
}
void wrong() {
	::result << 'W'; // เขียนไฟล์นั้นว่า "W"
	::exit(0);
}
int main(int argc, char* argv[]) {
	std::ifstream sol(argv[1]); 	//อ่าน .sol ไฟล์จาก argv ข้อที่ 1
	std::ifstream inp(argv[2]); 	//อ่าน .in ไฟล์จาก argv ข้อที่ 2
	std::ifstream cod(argv[3]); 	//อ่าน code จากไฟล์ที่น้องส่ง จาก argv ข้อที่ 3
	std::ifstream out("output.txt");//อ่าน output ที่น้อง Output ออกมา

    int num;
    out >> num;
    if (num <= 69) correct();
    else wrong();
}
```

### สคริปของพี่กฤษฏิ์

```cpp
#include <iostream>
#include <string>
#include <fstream>
#include <cstdlib>
#include <algorithm>


using namespace std;

std::ofstream result("grader_result.txt");
void correct() {
	::result << 'P';
	::exit(0);
}
void wrong() {
	::result << 'W';
	::exit(0);
}
int main(int argc, char* argv[]) {
	std::ifstream sol(argv[1]); 	//OPEN SOLUTION FILE
	std::ifstream inp(argv[2]); 	//OPEN INPUT    FILE
	std::ifstream cod(argv[3]); 	//OPEN SOURCE   FILE
	std::ifstream out("output.txt");//OPEN USER'S OUTPUT FILE

#ifdef __NOT_ALLOW_CPP__
	if (char c = std::string(argv[3]).back()
		; c == 'p' || c == 'P') ::wrong();
#endif
#ifdef __SOURCE_LIMMIT__
	cod.seekg(0, std::ios_base::end);
	if (cod.tellg() > __SOURCE_LIMMIT__) ::wrong();
	cod.clear();
	cod.seekg(0, std::ios_base::beg);
#endif
#ifdef __RESERVE_WORD__
	std::string code, tmp;
	while (cod >> tmp) code += tmp;
	code.erase(std::remove(code.begin(), code.end(), '\\'), code.end());
	for (auto [str, cnt] : std::map<std::string, int>__RESERVE_WORD__) {
		for (auto it = code.begin(); it != code.end(); ++it) {
			if (std::equal(str.begin(), str.end(), it) and !cnt--) {
				::wrong();
			}
		}
	}
#endif
	bool good_sol = true;
	bool good_out = true;
	std::string str_sol, str_out;
	std::ios_base::sync_with_stdio(0);
	do {
		good_sol = bool(sol >> str_sol);
		good_out = bool(out >> str_out);
	} while (good_sol and good_out and str_sol == str_out);
	if (!good_sol and !good_out) correct(); // read to end
	else wrong();
}
```

ซึ่งอาจดูเยอะ ดูยาว แต่สคริปนี้มีประโยชน์อยู่มากๆๆๆๆๆๆ

```cpp
...
#include <algorithm>

//#define __NOT_ALLOW_CPP__
//#define __SOURCE_LIMMIT__ 2000
//#define __RESERVE_WORD__ "for", "while", "loop"

using namespace std;
...
```

ทั้ง 3 การ `#define` เป็นการเพิ่มเงื่อนไขโจทย์เพื่อให้ยากขึ้น โดย...

- `#define __NOT_ALLOW_CPP__` คือการห้ามส่งภาษา c++
- `#define __SOURCE_LIMMIT__` คือการกำหนดขนาดไฟล์ที่ส่ง _(มีหน่วยเป็น byte)_
- `#define __RESERVE_WORD__` คือการกำหนดคำต้องห้ามในโปรแกรม หากมีคำเหล่านั้นอยู่ จะทำให้ได้ -

## interactive_script.py

เป็นไฟล์ Script ที่มีการพัฒนาใหม่ โดยเริ่มจาก
โดยข้อมูลที่ `interactive_script.py` จะรู้คือ

1. ที่อยู่ของไฟล์ Output ที่ผู้ส่งได้ Output ออกจากโปรแกรม
2. ที่อยู่ของแฟ้มโจทย์
3. หมายเลขเทสเคส

- ในการส่งผลการตรวจ สามารถทำได้โดยการ `print()` คำตอบออกมาทางหน้าจอ
- โดย `print('P')` คือผ่าน
- โดย `print('-')` คือไม่ผ่าน

### ตัวอย่างโจทย์ (เดิม)

แมวของคุณชอบเลข 69 หรือต่ำกว่า หากเกินนั้น แมวของคุณจะไม่สนใจทันที

งานของคุณคือ ให้เขียนโปรแกรมแสดงตัวเลข 69 หรือต่ำกว่า บนหน้าจอ

#### Input

(ไม่มี)

#### Output

จำนวนเต็ม หนึ่งจำนวนที่น้อยกว่าหรือเท่ากับ 69

#### ตัวอย่าง check.cpp ของข้อนี้

```py
# interactive_script.py
import sys
outputPath   = sys.argv[1]
problemDir   = sys.argv[2]
atCase       = sys.argv[3]

with open(outputPath, "r") as f:
    num = int(f.read())

if num <= 69:
    print("P")
else:
    print("-")
```
