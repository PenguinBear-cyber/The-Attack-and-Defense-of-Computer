# wtf

[題目] pwnable.kr (https://pwnable.kr/play.php)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/wtf_topic.jpg)

[解題]
首先，查看題目提供的 wtf.py 程式檔案，如下:
```
#!/usr/bin/python2
import os, sys, time
import subprocess
from threading import Timer

TIME = 5

class MyTimer():
	timer=None
	def __init__(self):
		self.timer = Timer(TIME, self.dispatch, args=[])
		self.timer.start()
	def dispatch(self):
		print 'program is not responding... something must be wrong :('
		os._exit(0)

def pwn( payload ):
	p = subprocess.Popen('./wtf', stdin=subprocess.PIPE, stdout=subprocess.PIPE)
	p.stdin.write( payload )
	output = p.stdout.readline()
	return output

if __name__ == '__main__':
	print '''
	---------------------------------------------------
	-              Shall we play a game?              -
	---------------------------------------------------
	
	Hey~, I'm a newb in this pwn(?) thing...
	I'm stuck with a very easy bof task called 'wtf'
	I think this is quite easy task, however my
	exploit payload is not working... I don't know why :(
	I want you to help me out here.
	please check out the binary and give me payload
	let me try to pwn this with yours.

	                            - Sincerely yours, newb
	'''
	sys.stdout.flush()
	time.sleep(1)

	try:
		sys.stdout.write('payload please : ')
		sys.stdout.flush()		
		payload = raw_input()
		payload = payload.decode('hex')

		print 'thanks! let me try if your payload works...'
		sys.stdout.flush()

		time.sleep(1)
		MyTimer()
		result = pwn( payload )
		if len(result) == 0:
			print 'your payload sucks! :('
			print 'I thought you were expert... what a shame :P'
			sys.stdout.flush()
			os._exit(0)

		print 'hey! your payload got me this : {0}\n'.format(result)
		print 'I admit, you are indeed an expert :)'
		sys.stdout.flush()
	except:
		print 'please give your payload in hex encoded format..'
		sys.stdout.flush()
		os._exit(0)

	sys.stdout.flush()
	os._exit(0)
```
接著，將另一個檔案 wtf 進行反編譯，其中較有意義的 function 如下:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/wtf_maincode.jpg)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/wtf_fgetscode.jpg)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/wtf_wincode.jpg)

可以發現 wtf 有一處棧溢出和一處有符號整數比較漏洞，而且有一個 win 函數可直接讀取 flag。

而這題其實是程序的輸入緩衝區設置了全緩衝模式（即緩衝區裝滿後才會讀取）。因此，當代碼一次性輸入進程序時，程序會一直卡住，而分開兩次輸入就沒有問題（即先輸入 size，再輸入 poc）。用 4096 個字符填滿了輸入緩衝區。

* exploit.py

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/wtf_exploit.jpg)

最後，執行 exploit code 即可得到答案。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/wtf_final.jpg)

I admit, you are indeed an expert :)

[參考資料]
* https://r00tnb.github.io/2018/07/16/pwnable.kr-wtf/
