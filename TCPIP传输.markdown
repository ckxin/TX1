#TCP/IP上下位机(Ubuntu主机和TX1)通信实现#

by xin

TCP/IP传输一般通过套接字即socket来实现，程序分为客户端和服务端，本次要实现的是TX1向主机传输，因此TX1做客户端，主机做服务端。

##一.套接字常用函数介绍##

###1.socket()###

socket()函数原型

    #include<sys/types.h>  
    #include<sys/socket.h>  
    int socket(int domain,int type,int protocol);  
    
参数说明：
* domain：协议族，对于socket要使用AF_INET，表示使用ipv4地址
* type：套接字参数类型，设置为SOCK_STREAM或SOCK_DGRAM。一般使用SOCK_STREAM，因为它是基于TCP的，能保证数据正确传送到对方。SOCK_DGRAM是基于UDP的，无法保证数据正确传送到对方。如果大家使用UDP方式传输，要使用SOCK_DGRAM
* protocol：制定协议。常用协议有IPPROTO_TCP,IPPTOTO_UDP,IPPROTO_SCTP,IPPROTO_TIPC等，它们分别对应TCP,UDP,STCP,TIPC协议。TCP协议传入参数0即可，即自动选择domain和type参数对应的协议
* 返回值：返回一个套接字描述符，出错返回-1

###2.bind()###

bind()函数原型

    int bind(int sock_fd,struct sockaddr_in *my_addr, int addrlen);  
    
参数说明：
* sock_fd：套接字，传入的参数是创建socket()函数时的返回值
* my_addr：一个指向包含有本机IP地址及端口号等信息得到sockaddr类的指针
* addrlen：地址长度，传入sizeof(my_addr)即可
* 返回值：成功返回0，失败返回-1；

###3.listen()###

listen()函数原型：

    int listen(int sock_fd， int backlog);  

参数说明：
* sock_fd：同上，传入的参数是创建socket()函数时的返回值
* backlog：指定在请求队列中允许的最大请求数。进入的连接请求在使用系统调用accept()应答之前要在进入队列中等待。这个值* 是队列中最多可以拥有的请求的个数。大多数系统的缺省设置为20
* 返回值：成功返回0，失败返回-1


###4.connect()###

connect()函数原型：

    int connect(int sock_fd, struct sockaddr *serv_addr,int addrlen);  
    
参数说明：
* sock_fd：套接字，传入的参数是创建socket()函数时的返回值
* serv_addr：包含远端主机IP地址和端口号的指针
* addrlen：同上，传入sizeof(serv_addr)即可
* 返回值：成功返回0，失败返回-1

###5.accept()###

accept()函数原型：

    #include<sys/socket.h>  
    int accept(int sock_fd,(struct sockaddr*) addr,int* addrlen);  

参数说明：
* sock_fd：同上，传入的参数是创建socket()函数时的返回值
* addr：指向sockaddr_in变量的指针
* addrlen：地址长度，传入&sizeof(addr)即可。注意：这个accept()函数传入的是&sizeof(addr)而不是sizeof(addr)！！！
* 返回值：成功返回新的套接字描述符，错误返回-1

###6.send()###

send()函数原型：

    int send(int sockfd,const void* buf,int len,int flags);  

参数说明：
* sockfd：套接字，传入socket()返回值
* buf：指向发送的数据的指针
* len：数据的字节长度
* flags：特殊传输标识，其值多为0。具体值可以参看博客      http://183132459shp.blog.163.com/blog/static/43151635201212412117565/
* 返回值：返回实际发送的字节数，这可能比你实际想要发送的字节数少。如果返回的字节数比要发送的字节数少，你必须发送剩下的数据。出错返回-1

###7.recv()###

recv()函数原型：

    int recv(int sockfd,void *buf,int len,int flags)   

参数说明：
* sockfd：套接字，传入socket()的返回值
* buf：指向接收数据的指针
* len：数据的字节长度
* flags：特殊传输标志，其值多为0。具体值可以参看博客http://183132459shp.blog.163.com/blog/static/43151635201212412117565/
* 返回值：返回实际读取到缓冲区的字节数，出错返回-1

###8.fopen()###

fopen()函数原型
    
    #include <stdio.h>
    FILE * fopen(const char * path, const char * mode);
    
参数说明：
* 参数 path字符串包含欲打开的文件路径及文件名
* 参数 mode字符串则代表着流形态。
* 返回值：文件顺利打开后，指向该流的文件指针就会被返回。如果文件打开失败则返回 NULL，并把错误代码存在error中

**注：**关于mode一般选取二进制流的形式，具体mode形态可参见 https://baike.baidu.com/item/fopen

###9.fwrite()###

fwrite()函数原型：

    #include<stdio.h>  
    size_t fwrite(const void* buffer, size_t size, size_t count, FILE* stream);

参数说明：
* buffer：是一个指针，对fwrite来说，是要获取数据的地址；
* size：要写入内容的单字节数；
* count:要进行写入size字节的数据项的个数；
* stream:目标文件指针；
* 返回值：返回实际写入的数据项个数count。

###10.fread()###

fread()函数原型

    #include<stdio.h> 
    size_t fread ( void *buffer, size_t size, size_t count, FILE *stream) ;

参数说明：
* buffer：用于接收数据的内存地址
* size：要读的每个数据项的字节数，单位是字节
* count：要读count个数据项，每个数据项size个字节.
* stream：输入目标文件指针
* 返回值：返回真实读取的项数，若大于count则意味着产生了错误。另外，产生错误后，文件位置指示器是无法确定的

##二.字节序介绍##

字节序分为大端和小端，即Big endian和Little endian。

大端：地址低位存数据的高位，地址高位存数据的低位

小端：地址低位存数据的低位，地址高位存数据的高位

主机字节序：主机自己的字节序，有大端和小端两种

网络字节序：标准大端字节序

TCP/IP协议规定：网络数据流应采用大端字节序，即低地址高字节。 

由于两端的两个主机的大小端不一定相同，因此为了使这些网络数据具有更强的可移植性，使相同的代码在大端和小端主机上都能正常运行，我们可以调用以下库函数进行网络字节序和主机字节序的相关转换：

    #include<arpa/inet.h>//将主机字节序转换为网络字节序
    uint32_t htonl(uint32_t hostlong);//将32长整数从主机字节序转换为网络字节序，
                                  //如果主机字节序是小端，则函数会做相应大小
                                  //端转换后返回；如果主机字节序是大端，则函
                                  //数不做转换，将参数原封不动返回。。。下同
    uint16_t htons(uint16_t hostshort);//将网络字节序转换为主机字节序
    uint32_t ntohl(uint32_t netlong);
    uint16_t ntohs(uint16_t netshort);
    // h表示主机（host），n表示网络（net），l表示32位长整数，s表示16短整数。
    
##三.完整代码##

**服务器端：**

    #ifndef COMMON_H_
    #define COMMON_H_

    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>
    #include <string.h>
    #include <sys/types.h>
    #include <sys/stat.h>
    #include <sys/socket.h>
    #include <netinet/in.h>
    #include <iostream>
    #define PORT 6000
    #define LISTENQ 20
    #define BUFFSIZE 4096
    #define FILE_NAME_MAX_SIZE 512
    using namespace std;
    #include "common.h"
    int main(int argc, char **argv[])
    {
    //Create socket
    int sockfd,connfd;
    struct sockaddr_in svraddr,clientaddr;
    bzero(&svraddr,sizeof(svraddr));
    bzero(&clientaddr,sizeof(clientaddr));

    svraddr.sin_family=AF_INET;
    svraddr.sin_addr.s_addr=htonl(INADDR_ANY);
    svraddr.sin_port=htons(PORT);

    sockfd=socket(AF_INET,SOCK_STREAM,0);
    if(sockfd<0)
    {
        perror("socket");
        exit(1);
    }

    //bind
    if(bind(sockfd,(struct sockaddr*)&svraddr,sizeof(svraddr))<0)
    {
        perror("bind");
        exit(1);
    }

    //listen
    if(listen(sockfd,LISTENQ)<0)
    {
        perror("listen");
        exit(1);
    }

    while(1)
    {
        socklen_t clientaddrlen=sizeof(clientaddr);

        //accept
        connfd=accept(sockfd,(struct sockaddr*)&clientaddr,&clientaddrlen);
        if(connfd<0)
        {
            perror("connect");
            exit(1);
        }

        //recv file imformation
        char buff[BUFFSIZE];
        char filename[FILE_NAME_MAX_SIZE];
        int count;
        bzero(buff,BUFFSIZE);
        count=recv(connfd,buff,BUFFSIZE,0);

        cout<<"count="<<count<<endl<<endl;
        //此处为接收到的文件名称大小，如果太大可能是发送时选择发送长度不正确，会影响后面文件内容接收，因为recv一次性接收                      最大是有限制的

       if(count<0)
        {
            perror("recv");
            exit(1);
        }
        strncpy(filename,buff,strlen(buff)>FILE_NAME_MAX_SIZE?FILE_NAME_MAX_SIZE:strlen(buff));
        printf("Preparing recv file : %s\n",filename);

        //recv file
        FILE *fd=fopen("/home/xin/cuda-workspace/TCPtest/123","wb+");
        //此处前面为文件要保存的路径，现在只能接收一次改动一次，后面还会改动让接收到的文件存在一个固定目录下
        if(NULL==fd)
        {
            perror("open");
            exit(1);
        }
        bzero(buff,BUFFSIZE);

        int length=0;
        while(length=recv(connfd,buff,BUFFSIZE,0))

        {
            if(length<0)
            {
                perror("recv");
                exit(1);
            }
            //cout<<"length="<<length<<endl;
            int writelen=fwrite(buff,sizeof(char),length,fd);
            //cout<<"writelen="<<writelen<<endl;
            if(writelen<length)
            {
                perror("write");
                exit(1);
            }
            bzero(buff,BUFFSIZE);
        }
        printf("Receieved file:%s finished!\n",filename);
        bzero(filename,FILE_NAME_MAX_SIZE);
        fclose(fd);
        close(connfd);
    }
    close(sockfd);
    return 0;
    }


**客户端：**

    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>
    #include <string.h>
    #include <sys/types.h>
    #include <sys/stat.h>
    #include <sys/socket.h>
    #include <netinet/in.h>
    #include <arpa/inet.h>
    #include <fcntl.h>
    #include <iostream>
    #define PORT 6000
    #define BUFFSIZE 4096
    #define FILE_NAME_MAX_SIZE 512
    #include "common.h"

    using namespace std;

    int main(int argc, char *argv[])//三个参数，第一个为main函数路径，第二个参数为IP地址，第三个是要传送的                            filename路径及名字
    {
    int clientfd;


    if(argc != 3)
    {
        fprintf(stderr,"Usage:./fileclient <IP_Address> <filename>\n");
        exit(1);
    }

    //Input the file name
    char filename[FILE_NAME_MAX_SIZE];
    bzero(filename,FILE_NAME_MAX_SIZE);

    strcpy(filename,argv[2]);

    struct sockaddr_in clientaddr;
    bzero(&clientaddr,sizeof(clientaddr));

    clientaddr.sin_family=AF_INET;
    clientaddr.sin_addr.s_addr=htons(INADDR_ANY);
    clientaddr.sin_port=htons(0);

    clientfd=socket(AF_INET,SOCK_STREAM,0);

    if(clientfd<0)
    {
        perror("socket");
        exit(1);
    }
    //cout<<"the clientfd="<<clientfd<<endl;
    //bind
    if(bind(clientfd,(struct sockaddr*)&clientaddr,sizeof(clientaddr))<0)
    {
        perror("bind");
        exit(1);
    }

    struct sockaddr_in svraddr;
    bzero(&svraddr,sizeof(svraddr));
    if(inet_aton(argv[1],&svraddr.sin_addr)==0)
    {
        perror("inet_aton");
        exit(1);
    }
    svraddr.sin_family=AF_INET;
    svraddr.sin_port=htons(PORT);

    socklen_t svraddrlen=sizeof(svraddr);
    if(connect(clientfd,(struct sockaddr*)&svraddr,svraddrlen)<0)
    {
        perror("connect");
        exit(1);
    }

    //send file imformation
    char buff[BUFFSIZE];
    int count;
    bzero(buff,BUFFSIZE);
    strncpy(buff,filename,strlen(filename)>FILE_NAME_MAX_SIZE?FILE_NAME_MAX_SIZE:strlen(filename));
    
    count=send(clientfd,buff,strlen(buff),0);//注意这里传送的长度一定是strlen(buff)，否则BUFFSIZE大于FILE_NAME_MAX_SIZE，接收时文件名只接收一次最多接收FILE_NAME_MAX_SIZE字节，会导致后面循环接收文件内容出现错误
    if(count<0)
    {
        perror("Send file information");
        exit(1);
    }

    //read file
    FILE *fd=fopen(filename,"a+");
    if(fd==NULL)
    {
        printf("File :%s not found!\n",filename);
    }
    else
    {
        bzero(buff,BUFFSIZE);
        int file_block_length=0;
        while((file_block_length=fread(buff,sizeof(char),BUFFSIZE,fd))>0)
        {
            printf("file_block_length:%d\n",file_block_length);
            if(send(clientfd,buff,file_block_length,0)<0)
            {
                perror("Send");
                exit(1);
            }
            bzero(buff,BUFFSIZE);
        }
        fclose(fd);
        printf("Transfer file finished !\n");
    }

    close(clientfd);
    return 0;
    }
    

另附**common.h**代码，注意保存成头文件添加到程序所在文件夹：

    #ifndef __common_h__
    #define __common_h__

    #include <sys/time.h>
    #include <assert.h>

    double GetTime() {
    struct timeval t;
    int rc = gettimeofday(&t, NULL);
    assert(rc == 0);
    return (double)t.tv_sec + (double)t.tv_usec/1e6;
    }

    void Spin(int howlong) {
    double t = GetTime();
    while ((GetTime() - t) < (double)howlong)
	; // do nothing in loop
	}

    #endif // __common_h__






