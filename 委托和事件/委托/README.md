# 委托
## 什么是委托
> 可以认为委托是持有一个或多个方法的对象。可以执行委托，这时委托会执行它所“持有”的方法。
## 委托使用的流程
> * 声明一个委托类型。委托的声明和方法的声明类似，但是没有实现块。
> * 使用该委托类型声明一个委托变量。
> * 创建委托型的对象，把它赋值给委托变量。新的委托对象包含指向某个方法的引用，这个方法和第一步定义的签名和返回类型一致。
> * 可以为委托对象增加其他方法。但这些方法必须与第一步中定义的委托类型有相同的签名和返回值类型。
> * 调用委托时，与调用方时一样的步骤，其包含的每一个方法都会执行。
> 
> ### 例
>
		delegate void MyDel(int x);//声明委托
	    class Program
	    {
	        void PrintLow(int value) {
	            Console.WriteLine("Value:{0}", value);
	        }
	        void PrintHight(int value)
	        {
	            Console.WriteLine("Value:{0}", value);
	        }
	        static void Main(string[] args)
	        {
	            Program program = new Program();
	            MyDel myDel;//声明委托类型的变量
	            Random random = new Random();
	            int randomValue = random.Next(99);//生成随机数
				//为委托变量赋值委托方对象	
	            myDel = randomValue < 50 ? new MyDel(program.PrintLow) : new MyDel(program.PrintHight);
	            myDel(randomValue);//执行委托	
	            Console.ReadKey();
	        }
	    }

## 声明委托类型
> ### 语法
>
		delegate void MyDel(int x);
> ![](https://i.imgur.com/axTl9cC.png)
> ### 注意
> * 委托类型的声明以`delegate`关键字开头
> * 没有方法主体

## 创建委托对象
> ### 语法
>
		MyDel delVar;
>![](https://i.imgur.com/BhREhRL.png)
> ### 注意
> * 有两种创建委托对象的方式。

>   + 第一种是使用带`new`运算符的对象创建表达式，如下所示
>
		delVar =new MyDel(myFunction);

>   + 第二种是快捷语法，它仅由说明符构成，如下所示
>
		delVar = myFunction;

> * 方法可以是实例方法或者静态方法

## 委托赋值
> 由于委托时引用类型，我们可以通过给他赋值来改变包含在委托变量中的引用。就得委托对象会被垃圾回收器回收。
> #### 例
>
		MyDel delVar;
		delVar = myFunction;//创建委托对象并赋值
		delVar = myfunction1;//创建新的对象并赋值

## 组合委托
> 委托可以使用额外的运算符来“组合”。
> #### 例
>
		MyDel delA=function1;
		MyDel delB=function2;
		MyDel delC=delA + delB;//组合调用列表
> ### 注意
> 委托是恒定的，委托对象被创建后不能再被改变。

## 为委托添加方法和移除方法
> 使用`+=`运算符为委托添加方法，使用`-=`运算符移除方法
> ###  添加方法
> #### 例
>
		MyDel delVar =myfunction;
		delVar += myfunction1;
		delVay += myfunction2; 

> #### 注意
> 由于委托是不可变的，所以委托的调用列表添加方法后的结果其实是变量指向的一个全新的委托
> ### 移除方法
> #### 例
>
		delVar -= myfunction1;
		delVay -= myfunction2; 	
>
> #### 注意
> * 与委托添加方法一样，其实是创建了一个新委托。新委托是旧委托的副本，只是没有了已经移除的方法的引用
> * 如果在调用列表中的方法有多个实例，`-=`运算符将从列表最后开始搜索，移除第一个与方法匹配的实例
> * 试图删除委托中不存在的方法没有效果
> * 试图调用空委托会抛异常，最好通过把委托与`null`进行比较来判断是否为空。

## 调用委托
> 可以像调用方法一样调用委托，用于调用委托的参数会用于调用列表中的每一个方法
> #### 例
>
		MyDel delVar =function;
		delVar +=function1;
		delVar +=function1;
		delVar(13);
> ### 注意
> 如果一个方法在调用列表中出现多次，当委托被调用时，每次在列表中遇到这个方法时它都会被调用一次。

## 调用带返回值的委托
> 如果委托有返回值并且在调用列表中有一个以上的方法，那么调用列表中最后一个方法返回的值就是委托调用返回的值，其他方法的值都会被忽略。
> #### 例
>
  		class IntDel_Class
	    {
	        int intValue = 5;
	        public int Add2()
	        {
	            intValue += 2;
	            return intValue;
	        }
	        public int Add3()
	        {
	            intValue += 3;
	            return intValue;
	        }
	    }
		delegate int IntDel();
		IntDel intDel = intDel_Class.Add2;
        intDel += intDel_Class.Add3;
        intDel += intDel_Class.Add2;
 		Console.WriteLine("带返回值委托：");
        Console.WriteLine("Value:{0}", intDel());
> 输出结果值为 12

## 调用带引用参数的委托
> 如果委托有引用参数，参数值会根据调用列表中的一个或多个方法的返回值而改变
> #### 例
> 
		class RefDel_Class
		{
		  public int Add2(ref int x)
		    {
		        x += 2;
		        return x;
		     }
		   public int Add3(ref int x)
		     {
		        x += 3;
		        return x;
		   	}
		}
		delegate int RefDel(ref int x);
		RefDel refDel = refDel_Class.Add2;
        refDel += refDel_Class.Add3;
        refDel += refDel_Class.Add2;
        int Intvalue = 5;
        refDel(ref Intvalue);//调用
        Console.WriteLine("带引用参数的委托：");
        Console.WriteLine("Value:{0}", Intvalue.ToString());
> 输出结果值为 12

## 匿名方法
> 如果方法只使用一次用来初始化委托时，除了委托语法的需要，没有必要创建独立的具体方法。匿名方法允许我们避免使用独立的具体方法名。
> #### 例
>
		 MyDel myDel = delegate (int x)
						 {
							 return x + 1;
						 };
		Console.WriteLine("{0}", myDel(12));
> ### 语法
>
		delegate （Parameters） {ImplementationCode}
> ![](https://i.imgur.com/QTGak3X.png)
> #### 返回值类型
> 匿名方法不会显示的声明返回值，在实现代码中必须返回一个在类型上与委托的返回值相同的值来匹配委托，如果委托有void类型的返回值，匿名方法就不能有返回值。
> #### 参数
> * 匿名方法的参数列表必须在参数的的数量，参数的类型及位置，修饰符三个方面与委托相匹配。
> * 当委托不包含任何out参数，且匿名方法不适用参数时，我们可以使圆括号为空或者省略来简化参数列表
>   + 例
>
		delegate void SomeDel (int X)
		SomeDel SDel = delegate
						{
							....
						};

> #### params参数
> * 如果委托声明的参数列表包含了`params`参数，那么匿名参数列表将忽略`params`关键字
>  + 例
>
		delegate void SomeDel (int X,params int [] Y)
		SomeDel SDel = delegate (int x,int [] y)
						{
							....
						};


## Lambda 表达式
> `Lambda`表达式简化了匿名方法的语法，从而避免了多余的信息。

> ### 语法
>
		MyDel del=(int x)=>{return x+1;};
> ### 注意
> * `Lambda`表达式删除了`delegate`关键字，参数列表和匿名方法主体之间放`Lambda`运算符`=>`,`Lambda`运算符读作"goes to"。
> * 编译器可以从委托的声明中知道委托的参数类型，因此`Lambda`表达式允许我们省略类型参数
>   + 带有类型的参数列表称为显式类型
>   + 省略类型的参数列表称为隐式类型
> * 如果只有一个隐式类型参数，可以省略圆括号
> * `Lambda`表达式允许表达式的主体是语句块或表达式，如果语句块包含了一个返回语句，我们可以将语句块替换为`return`关键字后的表达式
> * 如果没参数，必须使用一组空的圆括号。
> #### 例
>
		MyDel myDel = delegate (int x) { return x + 1; };//匿名方法
            MyDel del1 = (int x) => { return x + 1; };//Lambda表达式
            MyDel del2 = (x) => { return x + 1; };//Lambda表达式
            MyDel del3 = x => { return x + 1; };//Lambda表达式
            MyDel del4 = x => x + 1;//Lambda表达式
            Console.WriteLine("{0}", myDel(12));
            Console.WriteLine("{0}", del1(12));
            Console.WriteLine("{0}", del2(12));
            Console.WriteLine("{0}", del3(12));
            Console.WriteLine("{0}", del4(12));