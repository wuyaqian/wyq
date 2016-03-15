/*下面是银行家算法头文件*/
#include<iostream>
#define M 3 //这里表示的是进程的数目
#define N 2	//表示的是资源的种类数
using namespace std;
class BankerAlgorithm
{
private:
	/*最大需求矩阵Max*/
	int Max[M][N] ;
	/*已经分配矩阵Allocation*/
	int Allocation[M][N];
	/*尚需矩阵Need*/
	int Need[M][N];
	/*可利用矩阵Available*/
	int Available[N];
public:
	BankerAlgorithm()
	{
		InitMax();
		InitAllocation();
		InitNeed();
		InitAvaliable();
	}
public:
	void InitMax()
	{
		cout << "---------初始化最大需求矩阵开始！--------" << endl;
		for (int i = 0; i < M; ++i)
		{
			for (int j = 0; j < N; ++j)
			{
				cin >> Max[i][j];
			}
		//	cout << endl;
		}
		cout << "---------初始化最大需求矩阵完成！--------" << endl;
		cout << endl;
	}
	void InitAllocation()
	{
		cout << "---------初始化已经分配矩阵开始！--------" << endl;
		for (int i = 0; i < M; ++i)
		{
			for (int j = 0; j < N; ++j)
			{
				cin >> Allocation[i][j];
			}
		}
		cout << endl;
		cout << "---------初始化已经分配矩阵完成！--------" << endl;
	}
	void InitNeed()
	{
		cout << "---------求尚需各类资源量的矩阵！--------" << endl;
		for (int i = 0; i < M; ++i)
		{
			for (int j = 0; j < N; ++j)
			{
				Need[i][j]= Max[i][j] - Allocation[i][j];
			}
		}
		cout << "---------尚需各类资源量矩阵计算完成！--------" << endl;
		cout << endl;
	}
	void InitAvaliable()
	{
		cout << "---------初始化可以用资源矩阵开始！--------" << endl;
		for (int i = 0; i < N; ++i)
		{
			cin >> Available[i];
		}
		cout << "---------初始化可以用资源矩阵完成！--------" << endl;
		cout << endl;
	}
	
	void Show()
	{
		cout << "----------最大所需资源矩阵--------" << endl;
		_PrintArray(Max);
		cout << "----------已分配资源矩阵--------" << endl;
		_PrintArray(Allocation);
		cout << "----------尚需各类资源矩阵--------" << endl;
		_PrintArray(Need);
		cout << "----------各类资源可用矩阵--------" << endl;
		_PrintArray1(Available);
		cout << endl;
	}
	int CheckSafety()
	{
		int Work[N];//定义Work并且对Work赋值为Available
		for (int i = 0; i < N; ++i)
		{
			Work[i] = Available[i];
		}
		bool Finish[M] = { false };//初始化所有进程都还未分配
		int count = 0;
		for (int i = 0; i < M; ++i)//次数
		{
			for (int j = 0; j < M; ++j)//从进程中寻找一个
			{
				if (Finish[j] == false)
				{
					int flag = 1;//设置标志位，假设初始值代表能分配
					int k = 0;
					for (; k < N; ++k)
					{
						if (Need[j][k] > Work[k])
						{
							flag = 0;//不能分配
						}
					}
					if (flag == 1)
					{
						Finish[j] = true;//表示已经分配；不再考虑
						for (int m = 0; m < N; ++m)
						{
							Work[m] = Work[m] + Allocation[j][m];
						}
						printf("进程序列为P%d -->", j);
						++count;
					}
				}
				
			}
		}
		if (count == M)
		{
			return 1;
		}
		return 0;
	}
	void Request()
	{
		int request[N];
		int procNum;

		cout << "请输入对第procNum个进程申请：";
		cin >> procNum;
		
		int i = 0;
		for (; i < N; ++i)
		{
			cin >> request[i];
			if (request[i] > Need[procNum][i] )
			{
				cout << "Request不满足Need，不能分配" << endl;
				return;
			}
			if (request[i] > Available[i])
			{
				cout << "Request不满足Available，不能分配" << endl;
				return;
			}
		}
		//试分配
		int j = 0;
		for (; j < N; ++j)
		{
			Need[procNum][j] -= request[j];//立即得到满足，所需资源减少
			Available[j] -= request[j];//得到满足后，可用资源减少
			Allocation[procNum][j] += request[j];//得到满足后，已经分配的量增加
		}
		int ret = CheckSafety();
		if (ret == 0)//不安全
		{
			
			for (j = 0; j < N; ++j)
			{
				Need[procNum][j] += request[j];
				Available[j] += request[j];
				Allocation[procNum][j] -= request[j];
			}
			cout << "状态不安全，申请失败！" << endl;
		}
		//状态安全
		else
		{
			int flag = 1;
			for (int k = 0; k < N; ++k)
			{
				if (Need[procNum][k] != 0)//判断Need是否为0
				{
					flag = 0;
				}
			}
			if (flag == 1)//此时该进程的need为0
			{
				for (int m = 0; m < N; ++m)
				{
					Available[m] += Allocation[procNum][m];//将资源释放
					Allocation[procNum][m] = 0;
				}
			}
		}
	}
	
protected:
	void _PrintArray(int(*array)[N])
	{
		for (int i = 0; i < M; ++i)
		{
			for (int j = 0; j < N; ++j)
			{
				printf("P[%d]: %d	", i,array[i][j]);
			}
		}
		cout << endl;
	}
	void _PrintArray1(int * array1)
	{
		for (int i = 0; i < N; ++i)
		{
			printf("R[%d]: %d	",i, Available[i]);
		}
	}
};



/*以下是银行家算法的源文件*/
#include"BankAlgorithm.h"

void Menu()
{
	printf("*****************银行家算法测试开始******************\n");
	printf("*(0)Menu-> 0	     (1)安全性检验CheckSafety->1*****\n");
	printf("*(2)申请资源Request->2	      (3)显示资源状态Show->3*\n");
	printf("*(4)退出测试->4	      *******************************\n");
	printf("*****************银行家算法测试完成******************\n");
}
int main()
{
	BankerAlgorithm BA;//对资源的各项信息进行初始化
	while (1)
	{
		Menu();
		int chose;
		
		cout << "请选择功能键进行测试：" << endl;
		cin >> chose;
		int ret = 0;
		switch (chose)
		{
			case 0:
				Menu();//对资源的各项信息进行初始化
				break;
			case 1:
				//int ret = BA.CheckSafety(); //检查状态的安全性
				ret = BA.CheckSafety(); //检查状态的安全性
				if (ret == 1)
				{
				cout << "状态安全！" << endl;
				}
				else
				{
				cout << "状态不安全！" << endl;
				}	
				break;
			case 2:
				BA.Request();//对某进程申请各类资源
				break;
			case 3:
				BA.Show();//显示各类资源的状态信息
				break;
			case 4:
				exit(0);
			default:
				break;
		}
	}
	system("pause");
	return 0;
}
