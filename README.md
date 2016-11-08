# OScode
/*操作系统代码拷贝
*/

package Banker;
import java.util.*;
/**
 * 将老师的程序的重复部分放到了方法里
 * 改了序号，所有进程序号从0开始
 */
public class copybanker {
	public static void main(String[] arg){
		int process;//定义进程数量
		int resource=3;//定义资源种类是3
		int[] available;//可利用的资源
		int[][] max,allocation,need;//分别是最大的需求数、已分配的资源、需求资源
		Scanner scanner=new Scanner(System.in);
		System.out.print("请输入进程数>>");
		process=scanner.nextInt();
		System.out.print("请输入可利用资源向量（已定义3个资源种类）>>");
		available=new int[resource];
		for (int i = 0; i < resource; i++) {
			available[i]=scanner.nextInt();
		}
		System.out.println("请输入分配矩阵");
		allocation=new int[process][resource];
		for (int i = 0; i < process ; i++) {
//			System.out.print("请输入进程"+(i+1)+"已分配的资源数>>");
			System.out.print("请输入进程" + i + "已分配的资源数>>");
			for (int j = 0; j < resource; j++) {
				allocation[i][j]=scanner.nextInt();
			}
		}
		System.out.println("请输入最大需求矩阵");
		max=new int[process][resource];
		for (int i = 0; i <process ; i++) {
//			System.out.print("请输入进程"+(i+1)+"最大需求的资源数>>");
			System.out.print("请输入进程" + i + "最大需求的资源数>>");
			for (int j = 0; j < resource; j++) {
				max[i][j]=scanner.nextInt();
			}
		}
		
		need=new int[process][resource];
		for (int i = 0; i < process; i++) {
			for (int j = 0; j < resource; j++) {
				need[i][j]=max[i][j]-allocation[i][j];
			}
		}
		System.out.println();	
		/*
		 * 打印资源分配表
		 * */
		System.out.println("To时刻的资源分配表");
		System.out.println(" 进程   "
				+ "       max\t\tallocation\t  need\t\tavailable");
		for (int i = 0; i < process; i++) {
			System.out.print("P"+i+"\t");
			for (int j = 0; j < resource; j++) {
				System.out.print(max[i][j]+"   ");
			}
			System.out.print(" 	");
			for (int j = 0; j < resource; j++) {
				System.out.print(allocation[i][j]+"   ");
			}
			System.out.print(" 	");
			for (int j = 0; j < resource; j++) {
				System.out.print(need[i][j]+"   ");
			}
			if(i == 0){
				System.out.print(" 	");
				for (int k = 0; k <resource; k++) {
					System.out.print(available[k]+"   ");
				}
			}
			System.out.println();
		}
		checkSafety(resource,process,available,need,allocation);

		/**
		 * 以下是进程请求资源时的情况
		 * */
		boolean flag=true;
		while(flag){
			int[] req = new int[resource];
			System.out.print("请输入您要请求资源的编号>>");
			int choose = scanner.nextInt();
			System.out.print("请输入该进程的请求向量>>");
			for (int i = 0; i < resource; i++) {
				req[i]=scanner.nextInt();
			}
			if(req[0] <= need[choose][0]&&req[1] <= need[choose][1]&&req[2] <= need[choose][2]){
					if(req[0] <= available[0]&&req[1] <= available[1]&&req[2] <= available[2]){
						/**
						 * 此时开始设置假设分配给当前资源时的初始情况
						 */
						for (int i = 0;i < resource;i++) {
							available[i] = available[i]-req[i];
							allocation[choose][i] = allocation[choose][i] + req[i];
							need[choose][i] = need[choose][i] - req[i];
						}
						flag = checkSafety(resource,process,available,need,allocation);
					
					}else{
						System.out.println("资源不够请等待！");
					}//第二个if结束
			}//第一个if结束
			else{
				System.out.println("请求资源已超过所需资源！");
			}
		}//while结束
//最外面两个大括号
	}

	public static boolean checkSafety(int resource,int process,int[] available,int[][] need,int[][] allocation) {
		boolean flag = true;
		/**
		 * 检查安全序列
		 * */
		int[] work=new int[3];//定义一个数组work用来存放可利用的资源数目
		for (int i = 0; i < work.length; i++) {
			work[i]=available[i];//初始化work
		}
		boolean[] finish=new boolean[process];//定义标志finish，表示分配资源的置为true，没有非配的置为false
		for (int i = 0; i < process; i++) {
			finish[i]=false;//初始化数组finish
		}
		int[] array=new int[process];//定义一个数组保存安全序列
		int num=1;
		int count1=1; 
		//外面循环5-1=4次
		while(num<process){
			for (int i = 0; i < process; i++) {
				for (int j = 0; j < 3; j++) {
					if(finish[i]==false){
						if(need[i][0]<=work[0]&&need[i][1]<=work[1]&&need[i][2]<=work[2]){
								for (int j2 = 0; j2 < resource; j2++) {
									work[j2]=work[j2]+allocation[i][j2];
								}
								finish[i]=true;
								array[count1-1]=i;
								count1++;
						}
					}
				}
			}
			num++;
		}
		int count=0;
		for (int i = 0; i < array.length; i++) {
			if(finish[i]==true){
				count++;
			}
		}
		if(count==process){
			System.out.println("存在一个安全序列：");
			for (int i = 0; i < array.length; i++) {
				System.out.print("P"+array[i]+" ");
			}
		}
		else{
			System.out.println("系统处于不安全状态！");
			flag = false;
			return flag;
		}
		System.out.println();
		return true;
	}
}	

