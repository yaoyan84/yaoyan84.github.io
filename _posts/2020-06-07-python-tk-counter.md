---
layout: post
title:  "python用tk写一个计算器"
date:   2020-06-07 12:07:34
categories: 
tags:  python
excerpt: 
---

python用tkinter模块写一个计算器，很久没用有点忘了，回一下。


```python
import tkinter as tk

class Application(tk.Frame):
    result=0
    cur_num = None
    method = None
    def __init__(self, master=None):
        super().__init__(master)
        self.master = master
        self.pack()
        self.master.title('计算器')
        self.create_widgets()

    def create_widgets(self):
        self.led = tk.Label(self,bg='white',width=30,text='READY')
        self.led.grid(row=0,column=0,columnspan=5)
        tk.Button(self,text=u'1',height=2, width=4, command=lambda : self.do(1)).grid(row=1,column=0)
        tk.Button(self,text=u'2',height=2, width=4, command=lambda : self.do(2)).grid(row=1,column=1)
        tk.Button(self,text=u'3',height=2, width=4, command=lambda : self.do(3)).grid(row=1,column=2)
        tk.Button(self,text=u'4',height=2, width=4, command=lambda : self.do(4)).grid(row=1,column=3)
        tk.Button(self,text=u'5',height=2, width=4, command=lambda : self.do(5)).grid(row=1,column=4)
        tk.Button(self,text=u'6',height=2, width=4, command=lambda : self.do(6)).grid(row=2,column=0)
        tk.Button(self,text=u'7',height=2, width=4, command=lambda : self.do(7)).grid(row=2,column=1)
        tk.Button(self,text=u'8',height=2, width=4, command=lambda : self.do(8)).grid(row=2,column=2)
        tk.Button(self,text=u'9',height=2, width=4, command=lambda : self.do(9)).grid(row=2,column=3)
        tk.Button(self,text=u'0',height=2, width=4, command=lambda : self.do(0)).grid(row=2,column=4)
        tk.Button(self,text=u'+',height=2, width=4, command=lambda : self.do('+')).grid(row=3,column=0)
        tk.Button(self,text=u'-',height=2, width=4, command=lambda : self.do('-')).grid(row=3,column=1)
        tk.Button(self,text=u'*',height=2, width=4, command=lambda : self.do('*')).grid(row=3,column=2)
        tk.Button(self,text=u'/',height=2, width=4, command=lambda : self.do('/')).grid(row=3,column=3)
        tk.Button(self,text=u'=',height=2, width=4, command=lambda : self.do('=')).grid(row=3,column=4)
        tk.Button(self,text=u'clear',height=1, width=4, command=lambda : self.do('cls')).grid(row=4,column=0)
        tk.Button(self,text=u'QUIT',height=1, width=4, command=self.master.destroy).grid(row=4,column=4)

    def do(self,n):
        if n in range(0,10):
            if self.cur_num:
                self.cur_num = self.cur_num * 10 + n
            else:
                self.cur_num = n
            self.led['text'] = self.cur_num
        if n in ['+','-','*','/','=']:
            if self.method == '+':
                self.method = n
                if self.cur_num:
                    self.result = self.result + self.cur_num
                    self.cur_num = None           
            elif self.method == '-':
                self.method = n
                if self.cur_num:
                    self.result = self.result - self.cur_num
                    self.cur_num = None
            elif self.method == '*':
                self.method = n
                if self.cur_num:
                    self.result = self.result * self.cur_num
                    self.cur_num = None 
            elif self.method == '/':
                self.method = n
                if self.cur_num:
                    self.result = self.result / self.cur_num
                    self.cur_num = None
                else:
                    self.result = self.result  
            elif self.method == '=':
                self.result = self.result
                self.cur_num = None
                self.method = n
            else:
                if self.cur_num is not None:
                    self.result = self.cur_num
                else:
                    self.result = 0  
                self.cur_num = None
                self.method = n   
            self.led['text'] = self.result
        if n == 'cls':
            self.result = 0
            self.cur_num = None
            self.method = None
            self.led['text'] = 'CLEAR OK!'
        print("-------")
        print("Method: " + str(self.method))
        print("Result: " + str(self.result))
        print("Cur_Nm: " + str(self.cur_num))


if __name__=='__main__':
    root = tk.Tk()
    app = Application(master=root)
    app.mainloop()
```

执行结果：

![](/images/posts/2020/Snipaste_2020-06-07_14-58-39.png)
