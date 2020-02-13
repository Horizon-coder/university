//university
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<time.h>
#include <windows.h>
char ip[600000][256];

int no=1;//防止进行范围查询时easy_srarch提前输出

int pos;//记录范围查询的初末位置（ip数组下标）

int yes=1;//范围查询时记录ip地址输入错误的情况

void store()//将文件信息存入数组ip
{
    int i=0;
    char *filename = "ip.txt";

    FILE *fp = fopen(filename,"r");

    while(fscanf(fp,"%s\n",ip[i])!=EOF)
    {
        i++;
    }
    fclose(fp);
}

void split(char *src, const char *separator, char **dest, int *num)//字符分割函数
{
    /*
        src 源字符串的首地址(buf的地址)
        separator 指定的分割字符
        dest 接收子字符串的数组
        num 分割后子字符串的个数
    */
    char *pNext;
    int count = 0;

    if (src == NULL || strlen(src) == 0) //如果传入的地址为空或长度为0，直接终止
        return;

    if (separator == NULL || strlen(separator) == 0) //如未指定分割的字符串，直接终止
        return;

    pNext = (char *)strtok(src,separator); //使用(char *)进行强制类型转换(否则出现指针错误)
    while(pNext != NULL)
    {
        *dest++ = pNext;
        ++count;
        pNext = (char *)strtok(NULL,separator);  //使用(char *)进行强制类型转换
    }
    *num = count;
}

int isInt(char str[256])//判断字符串是否为全int型数据
{
    int t1;
    char temp[256];
    t1 = atoi(str);
    itoa(t1,temp,10);
    if(strcmp(str,temp) != 0)
    {
        return 1;//不是返回1
    }
    else
    {
        return 0;//是返回0
    }
}

void easy_search(char input[])//从文件中读取数据
{
    int begintime,endtime;//起始时间
    store();

    int left=0,right=586176;
    int mid=(left+right)/2;

    char *revbuf[7] = {0}; //存放分割后的子字符串
    int num = 0;//分割后子字符串的个数

    char *revleftaddress[4] = {0}; //存放分割后的首ip地址
    int leftnum = 0;

    char *revrightaddress[4] = {0}; //存放分割后的尾ip地址
    int rightnum = 0;

    char *revinputaddress[4] = {0}; //存放分割后的查找的ip地址
    int inputnum = 0;

    char *revtemp[4] = {0}; //存放分割后的查找的ip地址
    int tempnum = 0;
    split(input,".",revinputaddress,&inputnum); //调用函数进行分割
    if(atoi(revinputaddress[0]) >= 0 && atoi(revinputaddress[0]) <= 255 && isInt(revinputaddress[0]) == 0)
    {
        if(atoi(revinputaddress[1]) >= 0 && atoi(revinputaddress[1]) <= 255 && isInt(revinputaddress[1]) == 0)
        {
            if(atoi(revinputaddress[2]) >= 0 && atoi(revinputaddress[2]) <= 255 && isInt(revinputaddress[2]) == 0)
            {
                if(atoi(revinputaddress[3]) >= 0 && atoi(revinputaddress[3]) <= 255 && isInt(revinputaddress[3]) == 0)
                {
                    begintime = clock();	//计时开始
                    while (1)//进行二分查找
                    {
                        char temp[256]= {0};
                        strcpy(temp,ip[mid]);
                        char savedata[256]= {0};
                        strcpy(savedata, ip[mid]);
                        split(ip[mid],"|",revbuf,&num); //调用函数进行分割
                        split(revbuf[0],".",revleftaddress,&leftnum); //调用函数进行分割
                        split(revbuf[1],".",revrightaddress,&rightnum); //调用函数进行分割
                        if( (atoi(revinputaddress[0]) <atoi(revleftaddress[0])) )
                        {
                            right=mid-1;
                            mid=(left+right)/2;
                            continue;
                        }
                        else if((atoi(revinputaddress[0]) >atoi(revrightaddress[0])))
                        {
                            left=mid+1;
                            mid=(left+right)/2;
                            continue;
                        }
                        else
                        {
                            if( (atoi(revinputaddress[1]) <atoi(revleftaddress[1])) )
                            {
                                right=mid-1;
                                mid=(left+right)/2;
                                continue;
                            }
                            else if((atoi(revinputaddress[1]) >atoi(revrightaddress[1])))
                            {
                                left=mid+1;
                                mid=(left+right)/2;
                                continue;
                            }
                            else if(atoi(revrightaddress[1])!=atoi(revleftaddress[1]))
                            {
                                split(temp,"|",revtemp,&tempnum); //调用函数进行分割
                                pos=mid;
                                if(no)//no=0时为range_search调用的时候不需要输出
                                {
                                    printf("\n===========查询信息===========\n");
                                    printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                    printf("        起始地址        结束地址         国家              省份              城市\t\t\t\tISP\n");
                                    printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                    printf("%16s%16s%13s%18s%17s%33s\n",revtemp[0],revtemp[1],revtemp[2],revtemp[4],revtemp[5],revtemp[6]);//16 16 13 20 17 35
                                    endtime = clock();	//计时结束
                                    printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                    printf("查询时间：%dms\n", endtime-begintime);
                                    printf("需要保存文件请按T,否则按F\n");
                                    char ipq[2];
                                    scanf("%s",ipq);
                                    if(ipq[0]=='T')
                                    {
                                        printf("请输入保存的文件名如（1.txt）:");
                                        char filename[20];
                                        scanf("%s",filename);

                                        FILE *fp2 = fopen(filename, "w");
                                        fprintf(fp2,"%s\n", savedata);
                                        printf("保存成功\n");
                                        fclose(fp2);
                                    }
                                }
                                break;
                            }
                            else
                            {
                                if( (atoi(revinputaddress[2]) <atoi(revleftaddress[2])) )
                                {
                                    right=mid-1;
                                    mid=(left+right)/2;
                                    continue;
                                }
                                else if((atoi(revinputaddress[2]) >atoi(revrightaddress[2])))
                                {
                                    left=mid+1;
                                    mid=(left+right)/2;
                                    continue;
                                }
                                else
                                {
                                    split(temp,"|",revtemp,&tempnum); //调用函数进行分割
                                    pos=mid;
                                    if(no)//no=0时为range_search调用的时候不需要输出
                                    {
                                        printf("\n===========查询信息===========\n");
                                        printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                        printf("        起始地址        结束地址         国家              省份              城市\t\t\t\tISP\n");
                                        printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                        printf("%16s%16s%13s%18s%17s%33s\n",revtemp[0],revtemp[1],revtemp[2],revtemp[4],revtemp[5],revtemp[6]);//16 16 13 18 17 33
                                        endtime = clock();	//计时结束
                                        printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                        printf("查询时间：%dms\n", endtime-begintime);
                                        printf("需要保存文件请按T，否则按F\n");
                                        char ipq[2];
                                        scanf("%s",ipq);
                                        if(ipq[0]=='T')
                                        {
                                            printf("请输入保存的文件名如（1.txt）:");
                                            char filename[20];
                                            scanf("%s",filename);
                                            FILE *fp2 = fopen(filename, "w");
                                            fprintf(fp2,"%s\n", savedata);
                                            printf("保存成功\n");
                                            fclose(fp2);
                                        }
                                    }
                                    break;
                                }
                            }
                        }

                    }


                    return;
                }
            }
        }
    }
    printf("ip输入错误!\n");
    yes=0;//记录ip输入错误的情况，在range_search中输出
    return ;
}

void range_search()//从文件中读取数据
{
    no=0;
    yes=1;

    int begintime,endtime;

    int j;//遍历数组输出的变量

    int start=0,quit=0;//记录查询范围的初末位置（数组下标）
    int number;

    char *revbuf[7] = {0}; //存放分割后的子字符串


    char input[256];

    char *revinputaddress[2] = {0}; //存放分割后的查找的ip地址
    int inputnum = 0;

    int tempnum=0;


    printf("请输入ip地址(如219.235.224.1-219.236.1.1):");
    scanf("%s",input);

    split(input,"-",revinputaddress,&inputnum); //调用函数进行分割
    begintime = clock();	//计时开始
    easy_search(revinputaddress[0]);
    start=pos;
    if(yes)
    {
        easy_search(revinputaddress[1]);
        quit=pos;
        number=quit-start+1;
        endtime = clock();	//计时结束
        store();
        if(yes)
        {
            printf("数据数：%d 页数：%d(一页一百条数据)\n", number,number/100+1);
            printf("查询时间：%dms\n", endtime-begintime);
            printf("请输入要查询的页数（1-%d）,输入0表示结束:",number/100+1);
            while(1)
            {
                int page;
                scanf("%d",&page);
                if(page)
                {
                    printf("\n===========查询信息===========\n");
                    printf("-----------------------------------------------------------------------------------------------------------------------\n");
                    printf("        起始地址        结束地址         国家              省份              城市\t\t\t\tISP\n");
                    printf("-----------------------------------------------------------------------------------------------------------------------\n");
                    if(start+page*99>=quit)
                    {
                        for(j=start; j<=quit; j++)
                        {
                            char temp[256]= {0};
                            strcpy(temp,ip[j]);
                            split(temp,"|",revbuf,&tempnum); //调用函数进行分割
                            printf("%16s%16s%13s%20s%17s%35s\n",revbuf[0],revbuf[1],revbuf[2],revbuf[4],revbuf[5],revbuf[6]);//16 16 13 18 17 33
                            printf("-----------------------------------------------------------------------------------------------------------------------\n");

                        }

                    }
                    else
                    {
                        for(j=start; j<=(start+page*99); j++)
                        {
                            char temp[256]= {0};
                            strcpy(temp,ip[j]);
                            split(temp,"|",revbuf,&tempnum); //调用函数进行分割
                            printf("%16s%16s%13s%20s%17s%35s\n",revbuf[0],revbuf[1],revbuf[2],revbuf[4],revbuf[5],revbuf[6]);//16 16 13 18 17 33
                            printf("-----------------------------------------------------------------------------------------------------------------------\n");

                        }

                    }

                    printf("需要保存文件请按T，否则按F\n");
                    char ipq[2];
                    scanf("%s",ipq);
                    if(ipq[0]=='T')
                    {
                        printf("请输入保存的文件名如（1.txt）:");
                        char filename[20];
                        scanf("%s",filename);
                        FILE *fp2 = fopen(filename, "w");
                        for(j=start; j<=quit; j++)
                        {
                            fprintf(fp2,"%s\n", ip[j]);
                        }
                        printf("保存成功\n");
                        fclose(fp2);
                    }
                    no=1;
                    printf("请输入要查询的页数（1-%d）,输入0表示结束:",number/100+1);
                }
                else
                    return;
            }
        }

    }
    else
        return ;



}

struct data
{
    char start_add[256];
    char end_add[256];
    char nation[256];
    char area[256];
    char province[256];
    char city[256];
    char ISP[256];
} tempdata[100000];
int n = 0;

void sort(char str[])
{
    char *revtemp[7] = {0}; //存放分割后的子字符串
    int num = 0;//分割后子字符串的个数
    split(str,"|",revtemp,&num); //调用函数进行分割
    strcpy(tempdata[n].start_add	,revtemp[0]);
    strcpy(tempdata[n].end_add		,revtemp[1]);
    strcpy(tempdata[n].nation		,revtemp[2]);
    strcpy(tempdata[n].area			,revtemp[3]);
    strcpy(tempdata[n].province		,revtemp[4]);
    strcpy(tempdata[n].city			,revtemp[5]);
    strcpy(tempdata[n].ISP			,revtemp[6]);
}

void output()
{
    struct data tmp;
    int i,j;
    /***************按省份冒泡排序*******************/
    for(i=0; i<n-1; i++)
    {
        for(j=0; j<n-i-1; j++)
        {
            if(tempdata[j].province > tempdata[j+1].province)
            {
                tmp = tempdata[j];
                tempdata[j] = tempdata[j+1];
                tempdata[j+1] = tmp;
            }
        }
    }
    printf("\n===========查询信息===========\n");
    printf("-----------------------------------------------------------------------------------------------------------------------\n");
    printf("        起始地址        结束地址         国家              省份              城市\t\t\t\tISP\n");
    printf("-----------------------------------------------------------------------------------------------------------------------\n");
    for(i=0; i<n; i++)
    {
        printf("%16s%16s%13s%18s%17s%33s\n",tempdata[i].start_add,tempdata[i].end_add,tempdata[i].nation,tempdata[i].province,tempdata[i].city,tempdata[i].ISP);//16 16 13 18 17 33
        printf("-----------------------------------------------------------------------------------------------------------------------\n");
    }

}

void public_search(char input[])//从文件中读取数据
{
    char *filename = "ip.txt";
    char temp[256];
    char fileinput[256];
    FILE *fp = fopen(filename,"r");

    //用来接收返回数据的数组。这里的数组元素只要设置的比分割后的子字符串个数大就好了。

    char *revbuf[7] = {0}; //存放分割后的子字符串
    int num = 0;//分割后子字符串的个数

    char *revleftaddress[4] = {0}; //存放分割后的首ip地址
    int leftnum = 0;

    char *revrightaddress[4] = {0}; //存放分割后的尾ip地址
    int rightnum = 0;

    char *revinputaddress[4] = {0}; //存放分割后的查找的ip地址
    int inputnum = 0;

    split(input,".",revinputaddress,&inputnum); //调用函数进行分割
    if(atoi(revinputaddress[0]) >= 0 && atoi(revinputaddress[0]) <= 255 && isInt(revinputaddress[0]) == 0)
    {
        if(atoi(revinputaddress[1]) >= 0 && atoi(revinputaddress[1]) <= 255 && isInt(revinputaddress[1]) == 0)
        {
            if(atoi(revinputaddress[2]) >= 0 && atoi(revinputaddress[2]) <= 255 && isInt(revinputaddress[2]) == 0)
            {
                if(atoi(revinputaddress[3]) >= 0 && atoi(revinputaddress[3]) <= 255 && isInt(revinputaddress[3]) == 0)
                {
                    n=0;

                    while (fscanf(fp,"%s\n",fileinput)!= EOF)
                    {
                        strcpy(temp, fileinput);
                        char temp2[256]= {0};
                        strcpy(temp2, fileinput);
                        split(fileinput,"|",revbuf,&num); //调用函数进行分割
                        split(revbuf[0],".",revleftaddress,&leftnum); //调用函数进行分割
                        split(revbuf[1],".",revrightaddress,&rightnum); //调用函数进行分割
                        if( (atoi(revinputaddress[0]) >= atoi(revleftaddress[0])) && (atoi(revinputaddress[0]) <= atoi(revrightaddress[0])))
                        {
                            if((atoi(revinputaddress[1]) >= atoi(revleftaddress[1])) && (atoi(revinputaddress[1]) <= atoi(revrightaddress[1])))
                            {
                                if((atoi(revinputaddress[2]) >= atoi(revleftaddress[2])) && (atoi(revinputaddress[2]) <= atoi(revrightaddress[2])))
                                {
                                    if((atoi(revinputaddress[3]) >= atoi(revleftaddress[3])) && (atoi(revinputaddress[3]) <= atoi(revrightaddress[3])))
                                    {
                                        sort(temp);
                                        n++;


                                        goto a;
                                    }
                                }
                            }
                        }
                    }
a:
                    output();


                    fclose(fp);

                    return;
                }
            }
        }
    }
    printf("ip输入错误!\n");
}

void team_search(char str[])//从文件中读取数据
{
    int i;
    char *revbuf[100] = {0}; //存放分割后的子字符串
    int num = 0;//分割后子字符串的个数
    split(str," ",revbuf,&num); //调用函数进行分割

    for(i=0; i<num; i++)
    {
        public_search(revbuf[i]);
    }

}

void vague_search(char input[])//从文件中读取数据
{
    int begintime,endtime;

    int w=0;

    char *filename = "ip.txt";
    char temp[256];
    char fileinput[256];
    FILE *fp = fopen(filename,"r");

    //用来接收返回数据的数组。这里的数组元素只要设置的比分割后的子字符串个数大就好了。
    char *revbuf[7] = {0}; //存放分割后的子字符串
    int num = 0;//分割后子字符串的个数

    char *revleftaddress[4] = {0}; //存放分割后的首ip地址
    int leftnum = 0;

    char *revrightaddress[4] = {0}; //存放分割后的尾ip地址
    int rightnum = 0;

    char *revinputaddress[4] = {0}; //存放分割后的查找的ip地址
    int inputnum = 0;


    split(input,".",revinputaddress,&inputnum); //调用函数进行分割
    if(atoi(revinputaddress[0]) >= 0 && atoi(revinputaddress[0]) <= 255 && isInt(revinputaddress[0]) == 0)
    {
        if(atoi(revinputaddress[1]) >= 0 && atoi(revinputaddress[1]) <= 255 && isInt(revinputaddress[1]) == 0)
        {
            if(strcmp(revinputaddress[2], "*")==0)
            {
                if(strcmp(revinputaddress[3], "*")==0)
                {

                    n=0;
                    begintime = clock();	//计时开始
                    while (fscanf(fp,"%s\n",fileinput)!= EOF)
                    {
                        strcpy(temp, fileinput);

                        split(fileinput,"|",revbuf,&num); //调用函数进行分割
                        split(revbuf[0],".",revleftaddress,&leftnum); //调用函数进行分割
                        split(revbuf[1],".",revrightaddress,&rightnum); //调用函数进行分割

                        if( (atoi(revinputaddress[0]) >= atoi(revleftaddress[0])) && (atoi(revinputaddress[0]) <= atoi(revrightaddress[0])))
                        {
                            if((atoi(revinputaddress[1]) >= atoi(revleftaddress[1])) && (atoi(revinputaddress[1]) <= atoi(revrightaddress[1])))
                            {
                                sort(temp);
                                n++;
                                w++;
                            }
                        }
                    }
                    endtime = clock();	//计时结束
                    output();
                    fclose(fp);
                    printf("数据数：%d\n",w);
                    printf("查询时间：%dms\n", endtime-begintime);

                    return;

                }
            }
        }
    }
    printf("ip输入错误!\n");
}

void file_search(char str[])//从文件中读取数据
{
    int begintime,endtime;

    int w=0;

    char temp5[256];
    FILE *fp = fopen(str,"r");
    if (fp==NULL)
    {
        printf("文件不存在\n");
        fclose(fp);
        return;
    }
    else
    {
        printf("需要保存查询结果为文件请按T,否则按F\n");
        char ipq[2];
        scanf("%s",ipq);
        printf("\n===========查询信息===========\n");
        printf("-----------------------------------------------------------------------------------------------------------------------\n");
        printf("        起始地址        结束地址         国家              省份              城市\t\t\t\tISP\n");
        printf("-----------------------------------------------------------------------------------------------------------------------\n");
        if(ipq[0]=='T')
        {
            printf("请输入保存的文件名如（1.txt）:");
            char filename[20];
            scanf("%s",filename);

            FILE *fp2 = fopen(filename, "w");
            begintime = clock();	//计时开始

            while (fscanf(fp,"%s\n",temp5)!= EOF)
            {

                store();

                int left=0,right=586176;
                int mid=(left+right)/2;

                char *revbuf[7] = {0}; //存放分割后的子字符串
                int num = 0;//分割后子字符串的个数

                char *revleftaddress[4] = {0}; //存放分割后的首ip地址
                int leftnum = 0;

                char *revrightaddress[4] = {0}; //存放分割后的尾ip地址
                int rightnum = 0;

                char *revinputaddress[4] = {0}; //存放分割后的查找的ip地址
                int inputnum = 0;

                char *revtemp[4] = {0}; //存放分割后的查找的ip地址
                int tempnum = 0;
                split(temp5,".",revinputaddress,&inputnum); //调用函数进行分割
                if(atoi(revinputaddress[0]) >= 0 && atoi(revinputaddress[0]) <= 255 && isInt(revinputaddress[0]) == 0)
                {
                    if(atoi(revinputaddress[1]) >= 0 && atoi(revinputaddress[1]) <= 255 && isInt(revinputaddress[1]) == 0)
                    {
                        if(atoi(revinputaddress[2]) >= 0 && atoi(revinputaddress[2]) <= 255 && isInt(revinputaddress[2]) == 0)
                        {
                            if(atoi(revinputaddress[3]) >= 0 && atoi(revinputaddress[3]) <= 255 && isInt(revinputaddress[3]) == 0)
                            {
                                begintime = clock();	//计时开始

                                while (1)
                                {
                                    char temp[256]= {0};
                                    strcpy(temp,ip[mid]);
                                    char savedata[256]= {0};
                                    strcpy(savedata, ip[mid]);
                                    split(ip[mid],"|",revbuf,&num); //调用函数进行分割
                                    split(revbuf[0],".",revleftaddress,&leftnum); //调用函数进行分割
                                    split(revbuf[1],".",revrightaddress,&rightnum); //调用函数进行分割
                                    if( (atoi(revinputaddress[0]) <atoi(revleftaddress[0])) )
                                    {
                                        right=mid-1;
                                        mid=(left+right)/2;
                                        continue;
                                    }
                                    else if((atoi(revinputaddress[0]) >atoi(revrightaddress[0])))
                                    {
                                        left=mid+1;
                                        mid=(left+right)/2;
                                        continue;
                                    }
                                    else
                                    {
                                        if( (atoi(revinputaddress[1]) <atoi(revleftaddress[1])) )
                                        {
                                            right=mid-1;
                                            mid=(left+right)/2;
                                            continue;
                                        }
                                        else if((atoi(revinputaddress[1]) >atoi(revrightaddress[1])))
                                        {
                                            left=mid+1;
                                            mid=(left+right)/2;
                                            continue;
                                        }
                                        else if(atoi(revrightaddress[1])!=atoi(revleftaddress[1]))
                                        {
                                            split(temp,"|",revtemp,&tempnum); //调用函数进行分割
                                            printf("%16s%16s%13s%18s%17s%33s\n",revtemp[0],revtemp[1],revtemp[2],revtemp[4],revtemp[5],revtemp[6]);//16 16 13 18 17 33
                                            fprintf(fp2,"%s\n", savedata);
                                            w++;
                                            printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                            break;
                                        }
                                        else
                                        {
                                            if( (atoi(revinputaddress[2]) <atoi(revleftaddress[2])) )
                                            {
                                                right=mid-1;
                                                mid=(left+right)/2;
                                                continue;
                                            }
                                            else if((atoi(revinputaddress[2]) >atoi(revrightaddress[2])))
                                            {
                                                left=mid+1;
                                                mid=(left+right)/2;
                                                continue;
                                            }
                                            else
                                            {
                                                split(temp,"|",revtemp,&tempnum); //调用函数进行分割
                                                printf("%16s%16s%13s%18s%17s%33s\n",revtemp[0],revtemp[1],revtemp[2],revtemp[4],revtemp[5],revtemp[6]);//16 16 13 18 17 33
                                                fprintf(fp2,"%s\n", savedata);
                                                w++;
                                                printf("-----------------------------------------------------------------------------------------------------------------------\n");
                                                break;
                                            }
                                        }
                                    }

                                }
                                goto out;



                            }
                        }
                    }
                }
                printf("ip输入错误!\n");
            }
out:
            endtime = clock();	//计时结束
            fclose(fp);
            printf("数据数：%d\n",w);
            printf("查询时间：%dms\n", endtime-begintime);


            printf("保存成功\n");
            fclose(fp2);
        }
        else
        {


            while (fscanf(fp,"%s\n",temp5)!= EOF)
            {

                store();

                int left=0,right=586176;
                int mid=(left+right)/2;

                char *revbuf[7] = {0}; //存放分割后的子字符串
                int num = 0;//分割后子字符串的个数

                char *revleftaddress[4] = {0}; //存放分割后的首ip地址
                int leftnum = 0;

                char *revrightaddress[4] = {0}; //存放分割后的尾ip地址
                int rightnum = 0;

                char *revinputaddress[4] = {0}; //存放分割后的查找的ip地址
                int inputnum = 0;

                char *revtemp[4] = {0}; //存放分割后的查找的ip地址
                int tempnum = 0;
                split(temp5,".",revinputaddress,&inputnum); //调用函数进行分割
                if(atoi(revinputaddress[0]) >= 0 && atoi(revinputaddress[0]) <= 255 && isInt(revinputaddress[0]) == 0)
                {
                    if(atoi(revinputaddress[1]) >= 0 && atoi(revinputaddress[1]) <= 255 && isInt(revinputaddress[1]) == 0)
                    {
                        if(atoi(revinputaddress[2]) >= 0 && atoi(revinputaddress[2]) <= 255 && isInt(revinputaddress[2]) == 0)
                        {
                            if(atoi(revinputaddress[3]) >= 0 && atoi(revinputaddress[3]) <= 255 && isInt(revinputaddress[3]) == 0)
                            {
                                begintime = clock();	//计时开始
                                while (1)
                                {
                                    char temp[256]= {0};
                                    strcpy(temp,ip[mid]);
                                    split(ip[mid],"|",revbuf,&num); //调用函数进行分割
                                    split(revbuf[0],".",revleftaddress,&leftnum); //调用函数进行分割
                                    split(revbuf[1],".",revrightaddress,&rightnum); //调用函数进行分割
                                    if( (atoi(revinputaddress[0]) <atoi(revleftaddress[0])) )
                                    {
                                        right=mid-1;
                                        mid=(left+right)/2;
                                        continue;
                                    }
                                    else if((atoi(revinputaddress[0]) >atoi(revrightaddress[0])))
                                    {
                                        left=mid+1;
                                        mid=(left+right)/2;
                                        continue;
                                    }
                                    else
                                    {
                                        if( (atoi(revinputaddress[1]) <atoi(revleftaddress[1])) )
                                        {
                                            right=mid-1;
                                            mid=(left+right)/2;
                                            continue;
                                        }
                                        else if((atoi(revinputaddress[1]) >atoi(revrightaddress[1])))
                                        {
                                            left=mid+1;
                                            mid=(left+right)/2;
                                            continue;
                                        }
                                        else if(atoi(revrightaddress[1])!=atoi(revleftaddress[1]))
                                        {
                                            split(temp,"|",revtemp,&tempnum); //调用函数进行分割
                                            w++;
                                            printf("%16s%16s%13s%18s%17s%33s\n",revtemp[0],revtemp[1],revtemp[2],revtemp[4],revtemp[5],revtemp[6]);//16 16 13 18 17 33

                                            printf("-----------------------------------------------------------------------------------------------------------------------\n");

                                            break;
                                        }
                                        else
                                        {
                                            if( (atoi(revinputaddress[2]) <atoi(revleftaddress[2])) )
                                            {
                                                right=mid-1;
                                                mid=(left+right)/2;
                                                continue;
                                            }
                                            else if((atoi(revinputaddress[2]) >atoi(revrightaddress[2])))
                                            {
                                                left=mid+1;
                                                mid=(left+right)/2;
                                                continue;
                                            }
                                            else
                                            {
                                                split(temp,"|",revtemp,&tempnum); //调用函数进行分割
                                                w++;
                                                printf("%16s%16s%13s%18s%17s%33s\n",revtemp[0],revtemp[1],revtemp[2],revtemp[4],revtemp[5],revtemp[6]);//16 16 13 18 17 33

                                                printf("-----------------------------------------------------------------------------------------------------------------------\n");

                                                break;
                                            }
                                        }
                                    }

                                }
                                goto out9;



                            }
                        }
                    }
                }
                printf("ip输入错误!\n");
            }
out9:
            endtime = clock();	//计时结束
            fclose(fp);

            printf("数据数：%d\n",w);
            printf("查询时间：%dms\n", endtime-begintime);



        }
    }

}

void cmd_search(char str[])//从文件中读取数据
{
    char *revbuf[2] = {0}; //存放分割后的子字符串
    int num = 0;//分割后子字符串的个数
    split(str," ",revbuf,&num); //调用函数进行分割
    if(strcmp(revbuf[0], "file")==0)
    {
        file_search(revbuf[1]);
    }
    else if(strcmp(revbuf[0], "vague")==0)
    {
        vague_search(revbuf[1]);
    }
    else
    {
        printf("输入错误\n");
        return ;
    }
}

void menu()
{
    printf("-----------------------------------------【主菜单】--------------------------------\n");
    printf("\t\t\t\t\t0.退出系统\n");
    printf("\t\t\t\t\t1.单记录查询\n");
    printf("\t\t\t\t\t2.范围查询\n");
    printf("\t\t\t\t\t3.多组查询\n");
    printf("\t\t\t\t\t4.命令查询\n");
    printf("--------------------------------------------\n");
    printf("请选择菜单，输入（0-4）：");
}

void keyDown()
{
    char input[256];
    int userkey;
    scanf("%d",&userkey);
    getchar();//清空字符输入的缓冲区
    switch (userkey)
    {
    case 0:
        printf("\t\t【退出系统】\n");
        exit(0);
        break;
    case 1:
        printf("\t\t【单记录查询】\n");
        printf("请输入ip地址(如219.235.224.1):");
        scanf("%s",input);
        easy_search(input);
        break;
    case 2:
        printf("\t\t【范围查询】\n");

        range_search();

        break;
    case 3:
        printf("\t\t【多组查询】(不超过一百组数据)\n");
        printf("请输入ip地址(如219.235.224.1 219.236.1.1):");
        gets(input);

        team_search(input);

        break;
    case 4:
        printf("\t\t【命令查询】\n");
        printf("文件查询如:file 123.txt\n");
        printf("模糊查询如:vague 219.235.*.*(仅限此格式且按省份字典序排序)\n");
        printf("请输入命令:\n");
        gets(input);

        cmd_search(input);

        break;
    default:
        printf("输入错误，请重新输入!");
        break;
    }
}

void welcome0()
{
    printf("\n\n\n\n\n\n");
    printf("\t\t\t~*************欢迎进入ip地址查询系统*************~\n");
    printf("\n\n\n");
    printf("\t\t\t~*************制作者:  罗健淳 2020年2月*************~\n");
    printf("\n\n\n");
    printf("\t\t\t~*************按任意键进入主菜单*************~");
    while(getchar() == 0);
    system("cls");
    return ;
}

int main()
{
    system("color 0A");
    welcome0();
    while (1)
    {
        menu();
        keyDown();
        getchar();//清空字符输入的缓冲区
        system("pause");
        system("cls");
    }
}
