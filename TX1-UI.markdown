#TX1-UI#

by xin

使用QT实现一个简单的界面，可用于现场调试或观测时使用。

##一.安装QT Creator并配置OpenCV环境##   
安装及配置环境具体步骤参考：    
https://github.com/bclicn/how-to/tree/master/qt-opencv    
包含Windows及Ubuntu两种系统的安装教程，可根据自己系统自行选择，因为我在给TX1刷机时主机已安装OpenCV，所以我只安装了QT Creator，也就是说这两个安装过程都是独立的互不影响。    
在配置OpenCV库时需要注意：    
* 教程里只添加了四个库，我把主机上所有OpenCV库都加进去了，如果编译时报错，根据错误提示自行添加或删除即可。
* 添加库时要注意前面的注释，根据提示加上“Tab”或“-L”或“\”。


##二.实现界面##

####1.建立工程####

打开QT Creator新建一个Qt Widgets Application，点击choose，然后在Name栏对工程命名，Creat in栏选择工程创建目录，点击Next到达Kit Selection界面，一般应该会自动检测到，如果出现**“No Valid kits found.”**错误，可自行百度搜索解决办法，以下博客可供参考(仅供参考)：    
https://blog.csdn.net/sk490763125/article/details/51144422     
然后点击Next选择类，因为要实现一个桌面所以我选择了MainWindow类，基类也选择MainWindow，头文件和源文件会自动创建，Generate form根据需要勾选，勾选则生成界面文件，可利用QT自带的界面编辑器设计界面，但源代码好像无法改变，不勾选则需要自己写代码生成界面，优点就是代码自己写则各种属性可自由定义。点击Next然后点击Finish完成工程的创建。在工程文件中即“.pro”文件中配置好需要的库文件，即可开始编写代码。

####2.界面实现####

由于QT自带的界面编辑器局限性太大很不好用，因此最终选择自己写代码实现界面。    
代码包含五部分，主程序用来显示窗口并实现全屏显示，mainwindow用来写界面以及实现各种功能的槽函数，由于视频流的不断显示单线程会卡顿因此使用多线程，workthread用来开启新的线程并完成视频流的采集，多线程需要使用全局变量global用来定义全局变量，还有一个识别圆的程序Meter是另外师兄加进来的。所有代码如下所示：

**pro文件**

    #-------------------------------------------------
    #
    # Project created by QtCreator 2018-06-15T14:23:34
    #
    #-------------------------------------------------

    QT       += core gui

    greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

    TARGET = TX1ui
    TEMPLATE = app

    # The following define makes your compiler emit warnings if you use
    # any feature of Qt which has been marked as deprecated (the exact warnings
    # depend on your compiler). Please consult the documentation of the
    # deprecated API in order to know how to port your code away from it.
    DEFINES += QT_DEPRECATED_WARNINGS

    # You can also make your code fail to compile if you use deprecated APIs.
    # In order to do so, uncomment the following line.
    # You can also select to disable deprecated APIs only up to a certain version of Qt.
    #DEFINES += QT_DISABLE_DEPRECATED_BEFORE=0x060000    # disables all the APIs deprecated before             Qt 6.0.0


    SOURCES += \
            main.cpp \
            mainwindow.cpp \
        workthread.cpp \
        gloable.cpp \
        Meter.cpp

    HEADERS += \
            mainwindow.h \
        workthread.h \
        global.h \
        Meter.hpp
        




     INCLUDEPATH += /usr/include/opencv/
     INCLUDEPATH += /usr/include/opencv2/

     LIBS +=    -L/usr/lib/    \
        -lopencv_core  \
        -lopencv_imgproc   \
        -lopencv_ml \
        -lopencv_photo  \
        -lopencv_tegra  \
        -lopencv_video  \
        -lopencv_vstab  \
        -lopencv_legacy \
        -lopencv_core   \
        -lopencv_flann  \
        -lopencv_calib3d    \
        -lopencv_contrib    \
        -lopencv_gpu    \
        -lopencv_detection_based_tracker    \
        -lopencv_highgui    \
        -lopencv_imgproc    \
        -lopencv_imuvstab   \
        -lopencv_superres   \
        -lopencv_objdetect  \
        -lopencv_facedetect \
        -lopencv_features2d \
        -lopencv_esm_panorama   \
        -lopencv_stitching  \
        -lopencv_videostab  \
        -lopencv_highgui


**main.cpp**

    #include "mainwindow.h"
    #include <QApplication>
    #include <QLabel>
    #include <QPushButton>
    #include <QLayout>
    #include <iostream>
    #include <QDesktopWidget>
    #include <QScreen>
    #include <QApplication>
    #include <global.h>
    #include <opencv2/opencv.hpp>

    using namespace std;


    int main(int argc, char *argv[])
    {
        QApplication a(argc, argv);
        MainWindow w;
        w.show();
        w.showFullScreen();


        return a.exec();
    }
    
**mainwindow.h**    

    #ifndef MAINWINDOW_H
    #define MAINWINDOW_H
    
    #include <QMainWindow>
    #include <QLabel>
    #include <QPushButton>
    #include <QLayout>
    #include <workthread.h>
    
    class MainWindow : public QMainWindow
    {
        Q_OBJECT
    
    public:
        MainWindow(QWidget *parent = 0);
        ~MainWindow();
    private:
        QLabel *TimeLabel;
        QLabel *CameraLabel;
        QLabel *ImgLabel;
        QLabel *DataLabel;
        QLabel *DataLabel1;
    
        QPushButton *CameraBtn;
        QPushButton *GrabBtn;
        QPushButton *ShutdownBtn;
        QPushButton *RebootBtn;
    
        QHBoxLayout *TopLayout;
        QHBoxLayout *CameraLayout;
        QHBoxLayout *ImgLayout;
        QHBoxLayout *MiddleLayout1;
        QHBoxLayout *MiddleLayout2;
        QHBoxLayout *BottomLayout;
        WorkThread *workThread;
    
    /*public slots:
        void timerUpdate(void);*/
    
    public slots:
        void timerUpdate(void);
        void slotCamera();
        void slotGrab();
        void slotVideo();
        //void slotImgshow();
    private slots:
        //void on_CamreaBtn_clicked();
        //void on_GrabButton_clicked();
        void on_ShutdownBtn_clicked();
        void on_RebootBtn_clicked();
    };

    #endif // MAINWINDOW_H

**mainwindow.cpp**

    #include "mainwindow.h"
    #include <QLabel>
    #include <QPushButton>
    #include <QLayout>
    #include <QWidget>
    #include <QSizePolicy>
    #include <iostream>
    #include <QDesktopWidget>
    #include <QScreen>
    #include <QApplication>
    #include <QTimer>
    #include <qdatetime.h>
    #include <workthread.h>
    #include <unistd.h>
    #include <global.h>
    #include <opencv2/opencv.hpp>
    #include <QPixmap>
    #include <Meter.hpp>
    
    
    using namespace std;
    using namespace cv;
    using namespace bcv;
    
    
    
    MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    
    {
        QDesktopWidget *desktopWidget = QApplication::desktop();
        QRect screenRect = desktopWidget->screenGeometry();
        int g_nActScreenW = screenRect.width();
        int g_nActScreenH = screenRect.height();
    
        cout <<"width="<<g_nActScreenW<<endl;
        cout <<"height="<<g_nActScreenH<<endl;
    
    
        TimeLabel = new QLabel;
        int h0=g_nActScreenH*0.035;
        int w0=g_nActScreenW*0.98;
    
        TimeLabel->setFixedHeight(h0);
        TimeLabel->setFixedWidth(w0);
        TimeLabel->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);
        //TimeLabel->setSizePolicy(QSizePolicy::Expanding,QSizePolicy::Expanding);
        TimeLabel->setFrameStyle(QFrame::Panel|QFrame::Sunken);
        TimeLabel->setAlignment(Qt::AlignRight);
    
        TopLayout = new QHBoxLayout;
        TopLayout->addWidget(TimeLabel);
        int M_w0=g_nActScreenW*0.005;
        //int M_h0=g_nActScreenH*0.01;
        int M_h0=g_nActScreenH*0.02125;
        TopLayout->setContentsMargins(M_w0,M_h0,M_w0,M_h0);



        CameraLabel = new QLabel;
        CameraLabel->setFrameStyle(QFrame::Panel|QFrame::Sunken);
    
        int w1=g_nActScreenW*0.48;
        int h1=(w1/16)*9;
        CameraLabel->setFixedHeight(h1);
        CameraLabel->setFixedWidth(w1);

        CameraLabel->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);
        CameraLabel->setScaledContents(true);
        //int wid=CameraLabel->width();
        /*CameraLayout = new QHBoxLayout;
        CameraLayout->addWidget(CameraLabel);
    
        int M_w1=M_w0*0.5;
        int M_h1=M_h0*0.5;
        CameraLayout->setContentsMargins(M_w1,M_h1,M_w1,M_h1);*/
        //CameraLayout->setMargin(3);

        ImgLabel = new QLabel;
        ImgLabel->setFrameStyle(QFrame::Panel|QFrame::Sunken);
    
        //int w2=g_nActScreenW*0.48;
        //int h2=(w2/16)*9;
        ImgLabel->setFixedHeight(h1);
        ImgLabel->setFixedWidth(w1);
        ImgLabel->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);
        ImgLabel->setScaledContents(true);
        /*ImgLayout = new QHBoxLayout;
        ImgLayout->addWidget(ImgLabel);
        ImgLayout->setContentsMargins(M_w1,M_h1,M_w1,M_h1);*/
        //ImgLayout->setMargin(3);
    
        MiddleLayout1 = new QHBoxLayout;

        //MiddleLayout1->addLayout(ImgLayout);
        //MiddleLayout1->addLayout(CameraLayout);
    
        MiddleLayout1->addWidget(ImgLabel);
        MiddleLayout1->addWidget(CameraLabel);
    
        MiddleLayout1->setContentsMargins(M_w0,M_h0,M_w0,M_h0);
        int Space=g_nActScreenW*0.02;
        MiddleLayout1->setSpacing(Space);
        //MiddleLayout1->setMargin(5);
        //MiddleLayout1->setSpacing(5);



        DataLabel = new QLabel;
        DataLabel->setFrameStyle(QFrame::Panel|QFrame::Sunken);
        int w2=g_nActScreenW*0.48;
        int h2=g_nActScreenH*0.035;
        DataLabel->setFixedHeight(h2);
        DataLabel->setFixedWidth(w2);
        DataLabel->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);
        //DataLabel->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);

        DataLabel1 = new QLabel;
        DataLabel1->setFrameStyle(QFrame::Panel|QFrame::Sunken);
        int w3=g_nActScreenW*0.48;
        int h3=g_nActScreenH*0.035;
        DataLabel1->setFixedHeight(h3);
        DataLabel1->setFixedWidth(w3);
        //DataLabel1->setSizePolicy(QSizePolicy::Expanding,QSizePolicy::Expanding);
        DataLabel1->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);

        MiddleLayout2 = new QHBoxLayout;
        MiddleLayout2->addWidget(DataLabel);
        MiddleLayout2->addWidget(DataLabel1);
        MiddleLayout2->setContentsMargins(M_w0,M_h0,M_w0,M_h0);
        int Space1=g_nActScreenW*0.02;
        MiddleLayout2->setSpacing(Space1);



        int w4=g_nActScreenW*0.2;
        int h4=g_nActScreenH*0.2;
        CameraBtn = new QPushButton("Camera");
        CameraBtn->setFixedHeight(h4);
        CameraBtn->setFixedWidth(w4);
        //CameraBtn->setSizePolicy(QSizePolicy::Expanding,QSizePolicy::Expanding);
        CameraBtn->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);

        GrabBtn = new QPushButton("Grab");
        GrabBtn->setFixedHeight(h4);
        GrabBtn->setFixedWidth(w4);
        //GrabBtn->setSizePolicy(QSizePolicy::Expanding,QSizePolicy::Expanding);
        GrabBtn->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);

        ShutdownBtn = new QPushButton("Shutdown");
        ShutdownBtn->setFixedHeight(h4);
        ShutdownBtn->setFixedWidth(w4);
        //ShutdownBtn->setSizePolicy(QSizePolicy::Expanding,QSizePolicy::Expanding);
        ShutdownBtn->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);
    
        RebootBtn = new QPushButton("Reboot");
        RebootBtn->setFixedHeight(h4);
        RebootBtn->setFixedWidth(w4);
        //RebootBtn->setSizePolicy(QSizePolicy::Expanding,QSizePolicy::Expanding);
        RebootBtn->setSizePolicy(QSizePolicy::Fixed,QSizePolicy::Fixed);

        BottomLayout= new QHBoxLayout;
        BottomLayout->addWidget(CameraBtn);
        BottomLayout->addWidget(GrabBtn);
        BottomLayout->addWidget(ShutdownBtn);
        BottomLayout->addWidget(RebootBtn);
        BottomLayout->setContentsMargins(M_w0,M_h0,M_w0,M_h0);
        int Space2=g_nActScreenW*0.06;
        BottomLayout->setSpacing(Space2);


        QWidget *centerWindow = new QWidget;
        this->setCentralWidget(centerWindow);
        //centerWindow->setSizePolicy(QSizePolicy::Expanding,QSizePolicy::Expanding);
    
        QVBoxLayout *mainLayout = new QVBoxLayout(this);
        centerWindow->setLayout(mainLayout);
        mainLayout->setContentsMargins(0,0,0,0);
        mainLayout->setSpacing(0);
        mainLayout->addLayout(TopLayout);
        mainLayout->addLayout(MiddleLayout1);
        mainLayout->addLayout(MiddleLayout2);
        mainLayout->addLayout(BottomLayout);
        mainLayout->setSizeConstraint(QLayout::SetDefaultConstraint);
        /*centerWindow->addLayout(TopLayout);
        centerWindow->addLayout(MiddleLayout1);
        centerWindow->addLayout(MiddleLayout2);
        centerWindow->addLayout(BottomLayout);*/

        QTimer *timer = new QTimer(this);
        connect(timer,SIGNAL(timeout()),this,SLOT(timerUpdate()));
        timer->start(1000);
        connect(CameraBtn,SIGNAL(clicked()),this,SLOT(slotCamera()));
        connect(GrabBtn,SIGNAL(clicked()),this,SLOT(slotGrab()));
        connect(ShutdownBtn,SIGNAL(clicked()),this,SLOT(on_ShutdownBtn_clicked()));
        connect(RebootBtn,SIGNAL(clicked()),this,SLOT(on_RebootBtn_clicked()));

    }

    MainWindow::~MainWindow()
    {
    
    }


    void MainWindow::timerUpdate(void)
    {
        QDateTime time = QDateTime::currentDateTime();
        QString str = time.toString("yyyy-MM-dd hh:mm:ss dddd");
        TimeLabel->setText(str);
    }

    /*void MainWindow::CameraBtn_clicked()
    {
        slotCamera();
    
    }

    void MainWindow::GrabBtn_clicked()
    {
        slotGrab();

    }*/

    void MainWindow::on_ShutdownBtn_clicked()
    {
        system("shutdown -h now");
    
    }

    void MainWindow::on_RebootBtn_clicked()
    {
        system("shutdown -r -t 0");

    }

    void MainWindow::slotCamera()
    {
    
            workThread=new WorkThread();
            connect(workThread,SIGNAL(show(const QString)),this,SLOT(slotVideo()));

            workThread ->start();

            CameraBtn ->setEnabled(false);
        //ui ->stopBtn ->setEnabled(true);

    }

    void MainWindow::slotVideo()
    {
        //Meter m;
        //Mat rslt;
        Mat Rgb;
        QImage Img;
        //m.run(image,rslt);
        cv::cvtColor(image, Rgb, CV_BGR2RGB);//棰滆壊绌洪棿杞崲
        Img = QImage((const uchar*)(Rgb.data), Rgb.cols, Rgb.rows, Rgb.cols * Rgb.channels(),                                                                                         QImage::Format_RGB888);

        CameraLabel->setPixmap(QPixmap::fromImage(Img));
        CameraLabel->show();

    }

    void MainWindow::slotGrab()
    {
        //Mat rslt;
        Meter m;
        Mat rslt;
            if (writingImg){
                for(;;){
                    if(!writingImg){
                        break;
                    }
                }
            }
            cout << image.cols << "x" << image.rows << endl;
            m.run(image,rslt);
            imwrite("/home/xin/Qt/frame.jpg",rslt);
            //imwrite("/home/xin/Qt/frame.jpg",image);
            QPixmap pixmap("/home/xin/Qt/frame.jpg");
            ImgLabel->setPixmap(pixmap);
            ImgLabel->show();
            QString str="12345";
            DataLabel->setText(str);
            //w1=pixmap.size();
    }

**workthread.h**

    #ifndef WORKTHREAD_H
    #define WORKTHREAD_H
    #include <QThread>
    class  WorkThread : public QThread
    {
        Q_OBJECT
    public:
         WorkThread();

    signals:
        void show(const QString &name);


    protected:
         void run();
    };

    #endif // WORKTHREAD_H

**workthread.cpp**

    #include "workthread.h"
    #include <QtDebug>
    #include "mainwindow.h"
    //#include "ui_mainwindow.h"

    #include <iostream>
    #include <unistd.h>
    #include <stdio.h>
    #include <stdlib.h>
    #include <opencv2/opencv.hpp>
    #include <global.h>
    #include <Meter.hpp>



    using namespace std;
    using namespace cv;
    using namespace bcv;

    WorkThread::WorkThread()
    {
    
    }

    void WorkThread::run()

    {
        VideoCapture cap("rtsp://admin:12345678xin@192.168.1.64/h264/ch1/main/av_stream");


            for(;;)
            {
                bool OK = cap.grab();

                    if (OK == false)
                   {
                    cout << "cannot grab" << endl;
                   }
                    else
                    {

                        writingImg = true;
                        cap >> image;
                        writingImg = false;
                        emit show("show");
                        //emit pause(1);
                    }
            }

    }


**global.h**

    #ifndef GLOABLE
    #define GLOABLE

    #include <opencv2/opencv.hpp>

    extern cv::Mat image;
    extern bool writingImg;

    #endif // GLOABLE

**global.cpp**

    #include <global.h>
    #include "opencv2/core.hpp" // include opencv lib

    cv::Mat image;
    bool writingImg;

**Meter.h**

    /*
     * Meter.h
     *
     *  Created on: Jun 26, 2018
     *      Author: bcli
     */

    #ifndef METER_H_
    #define METER_H_

    #include "opencv2/core.hpp"

    namespace bcv {


    class Meter {
    public:
    	Meter (int maxWidth = 1920, int maxHeight = 1080, int it = 400, int offset = 5, int div =     4, int thres = 0);
    	~Meter ();
    
    	void run(cv::Mat& src, cv::Mat& rslt);
    	bool find(cv::Mat& src);

    	int centerX();
    	int centerY();
    	int radius();

    	friend std::ostream& operator<<(std::ostream& os, const Meter& cd);

    private:
    	bool guided(cv::Mat& src, cv::Mat& rslt, int r=7, float eps=0.04, float s=0.3);
    	void findPositivePoints(cv::Mat& src, const int thres);
    	void estimateCircles();
    	bool estimateCircle(const unsigned short& crId,
    						const unsigned short& ptId1,
    						const unsigned short& ptId2,
    						const unsigned short& ptId3);
    	unsigned short voteCircles();
    	unsigned short voteCircle(const unsigned short& crId);

    	unsigned short* x;
    	unsigned short* y;
    	unsigned short* a;
    	unsigned short* b;
    	unsigned short* r;
    	unsigned short* v;

    	unsigned short maxPts;
    	unsigned short maxCrs;
    
    	unsigned short ptCnt;
    	unsigned short crCnt;

    	unsigned short maxRadius;
    	unsigned short maxVotedCrId;

    	char offset;
    	char thres;
    	char div;

    	int center_x;
    	int center_y;
    	int circle_radius;
    };

    } /* namespace bcv */

    #endif /* METER_H_ */


**Meter.cpp**

    /*
     * Meter.cpp
     *
     *  Created on: Jun 26, 2018
     *      Author: bcli
     */

    #include "Meter.hpp"
    #include "opencv2/core.hpp"
    #include "opencv2/imgproc.hpp"
    #include <iostream>

    using namespace cv;
    using namespace std;

    namespace bcv {

    Meter::Meter (int maxWidth, int maxHeight, int it, int offset, int div, int thres){

    	if (it <= 0){
    		cout << "Meter::Meter() 'it' has been reset to 400" << endl;
    		it = 400;
    	}

    	if (thres < 0 || thres > 255){
    		cout << "Meter::Meter() 'thres' has been reset to 0" << endl;
    		thres = 0;
    	}

    	if (offset < 0){
    		cout << "CircleDtector::Meter() 'offset' has been reset to 5" << endl;
    		offset = 5;
    	}

    	maxPts = (maxWidth*maxHeight)/div;
    	maxCrs = it;

    	x = new unsigned short[maxPts];
    	y = new unsigned short[maxPts];
    	a = new unsigned short[maxCrs];
    	b = new unsigned short[maxCrs];
    	r = new unsigned short[maxCrs];
    	v = new unsigned short[maxCrs];

    	ptCnt = 0;
    	crCnt = 0;

    	maxRadius = maxWidth/2;
    	maxVotedCrId = 0;

    	center_x = 0;
    	center_y = 0;
    	circle_radius = 0;

    	this->thres = thres;
    	this->offset = offset;
    	this->div = div;
    }

    Meter::~Meter () {

    	delete [] x;
    	delete [] y;
    	delete [] a;
    	delete [] b;
    	delete [] r;
    	delete [] v;
    }

    void Meter::run(Mat& src, Mat& rslt){
    	Mat gdd,can;
    	guided(src,gdd);
    	Canny(gdd,can,30,60);
    	find(can);
    	rslt = src.clone();
    	circle(rslt,Point(centerX(),centerY()),radius(),Scalar(0,255,255),5);
    }

    bool Meter::find(Mat& src){
    	if(src.type() != CV_8UC1){
    		cout << "Meter::find() requires a single channel binarized image (CV_8UC1)" << endl;
    		return false;
    	}
    	findPositivePoints(src, thres);
    	estimateCircles();
    	voteCircles();
    	center_x = a[maxVotedCrId];
    	center_y = b[maxVotedCrId];
    	circle_radius = r[maxVotedCrId];
    	return true;
    }

    int Meter::centerX(){
    	return center_x;
    }

    int Meter::centerY(){
    	return center_y;
    }

    int Meter::radius(){
    	return circle_radius;
    }

    void Meter::findPositivePoints(Mat& src, int thres){
    	int cols = src.rows;
    	int rows = src.cols;
    	ptCnt = 0;
    	for(int j=0;j<cols;j++){
    		uchar* data = src.ptr<uchar>(j);
    		for (int i=0;i<rows;i++){
    			if (data[i] > thres){
    					if (ptCnt >= maxPts) {break;}
    					x[ptCnt] = i;
    					y[ptCnt] = j;
    					ptCnt++;
    			}
    		}
    	}
    }

    void Meter::estimateCircles(){
    	srand(time(NULL));		// random seed
    	unsigned short max = ptCnt - 1;
    	unsigned short rand1,rand2,rand3;
    	crCnt = 0;

    	for(unsigned short i = 0; i < maxCrs; i++){
    		rand1 = rand()%max +1;
    		rand2 = rand()%max +1;
    		rand3 = rand()%max +1;
    		if (estimateCircle(i,rand1,rand2,rand3)) crCnt++;
    	}
    }

    bool Meter::estimateCircle(const unsigned short& crId,
    									const unsigned short& ptId1,
    									const unsigned short& ptId2,
    									const unsigned short& ptId3){
    	int x1 = x[ptId1];
    	int y1 = y[ptId1];
    	int x2 = x[ptId2];
    	int y2 = y[ptId2];
    	int x3 = x[ptId3];
    	int y3 = y[ptId3];
    	int a1,a2,a3;
    	double a,b,r;
    	a1 = x1*x1*y3 + x2*x2*y1 + x2*x2*y2 + x3*x3*y2 + y1*y2*y2 + y1*y1*y3 + y2*y3*y3;
    	a2 = x1*x1*y2 + x2*x2*y2 + x2*x2*y3 + x3*x3*y1 + y1*y1*y2 + y1*y3*y3 + y2*y2*y3;
    	a3 = 2*((x1-x2)*(y3-y2) + (x2-x3)*(y1-y2));
    	if (a3 == 0){
    		this->a[crId] = 0;
    		this->b[crId] = 0;
    		this->r[crId] = 0;
    		return false;
    	}
    	a = (a1-a2)/a3;
    	b = (x1*x1 - x2*x2 + y1*y1 - y2*y2 - 2*a*(x1-x2))/(2*(y1-y2));
    	r = sqrt((x2-a)*(x2-a) + (y2-b)*(y2-b));
    	this->a[crId] = round(a);
    	this->b[crId] = round(b);
    	this->r[crId] = round(r);
    	return true;
    }

    unsigned short Meter::voteCircles (){
    	int vote = 0;
    	int maxVote = 0;
    

    	for(unsigned short i = 0; i < maxCrs; i++){
    		if (r[i] > maxRadius){
    			v[i] = 0;
    			continue;
    		}
    		v[i] = voteCircle(i);
    		if (v[i] > maxVote){
    			maxVote = v[i];
    			maxVotedCrId = i;
    		}
    	}
    	return maxVotedCrId;
    }

    unsigned short Meter::voteCircle(const unsigned short& crId){
    	unsigned short a = this->a[crId];
    	unsigned short b = this->b[crId];
    	unsigned short r = this->r[crId];
    	unsigned short x,y,dis,vote = 0;
    
    	for(unsigned short i = 0; i < ptCnt; i++){
    		x = this->x[i];
    		y = this->y[i];
    		dis = round(sqrt((a-x)*(a-x) + (b-y)*(b-y)));
    		if (dis > (r-offset) && dis < (r+offset)){
    			vote++;
    		}
    	}
    	return vote;
    }

    ostream& operator<<(ostream& os, const Meter& cd){
    	os << "it:" << cd.maxCrs << ", off:" << (int)cd.offset << ", div:" << (int)cd.div << ",     thres:" << (int)cd.thres
    	   <<", pts:" << cd.ptCnt << "/" << cd.maxPts << ", crs:" << cd.crCnt
    	   << ", cr:(" << cd.center_x << "," << cd.center_y << ") " << cd.circle_radius << ", vt:"     << cd.v[cd.maxVotedCrId]
    	   << ", id:" << cd.maxVotedCrId;
    	return os;
    }

    bool Meter::guided(Mat& src, Mat& rslt, int r, float eps, float s){
    	if(src.type() != CV_8UC3){
    		cout << "Filter::find() requires 3 channel BGR image (CV_8UC3)" << endl;
    		return false;
    	}
    	Mat I = src.clone();
    	Mat p = src.clone();
    	cvtColor(I,I,CV_BGR2GRAY);
    	cvtColor(p,p,CV_BGR2GRAY);
    	I.convertTo(I,CV_64FC1,1.0/255);
    	p.convertTo(p,CV_64FC1,1.0/255);

    	int rows = I.rows;
    	int cols = I.cols;
    
    	Mat small_I, small_p;

    	Size smallSize(int(round(s*cols)), int(round(s*rows)));

    	resize(I,small_I,smallSize,0,0,CV_INTER_CUBIC);
    	resize(p,small_p,smallSize,0,0,CV_INTER_CUBIC);

    	int small_r = int(round(r*s));
    	Size winSize(2*small_r + 1, 2*small_r + 1);

    	Mat mean_small_I, mean_small_p;
    	boxFilter(small_I, mean_small_I, CV_64FC1,winSize);
    	boxFilter(small_p, mean_small_p, CV_64FC1,winSize);

    	Mat small_Ip = small_I.mul(small_p);
    	Mat mean_small_Ip;
    	boxFilter(small_Ip, mean_small_Ip, CV_64FC1,winSize);

    	Mat cov_small_Ip = mean_small_Ip - mean_small_I.mul(mean_small_p);

    	Mat mean_small_II;
    	boxFilter(small_I.mul(small_I), mean_small_II, CV_64FC1,winSize);

    	Mat var_small_I = mean_small_II - mean_small_I.mul(mean_small_I);
    	Mat small_a = cov_small_Ip/(var_small_I + eps);
    	Mat small_b = mean_small_p - small_a.mul(mean_small_I);

    	Mat mean_small_a, mean_small_b;
    	boxFilter(small_a, mean_small_a, CV_64FC1, winSize);
    	boxFilter(small_b, mean_small_b, CV_64FC1, winSize);

    	Mat mean_a, mean_b;
    	resize(mean_small_a, mean_a, I.size(), 0,0,CV_INTER_LINEAR);
    	resize(mean_small_b, mean_b, I.size(), 0,0, CV_INTER_LINEAR);

    	Mat q = mean_a.mul(I) + mean_b;
    	q.convertTo(rslt,CV_8UC3,255);
    	//Mat I_enhanced = (I-q)*5 + q;
    	//normalize(I_enhanced, I_enhanced,1,0,NORM_MINMAX, CV_32FC1);

    	//I_enhanced.convertTo(rslt,CV_8UC3,255);
    	return true;
    }


    } /* namespace bcv */
    
                                                                                                                

**注：**界面各控件的大小、控件间的间隔(Space)及各布局的边距(Margin)，是由代码先获取屏幕大小，然后各自设置为屏幕宽高的确定的百分比，而所有控件的宽高加上Space和Margin正好等于屏幕的宽高，然而这样始终无法实现全屏显示，开始我不知道为什么，直到后来我才想明白，因为最后各布局要添加到一个主布局里面，而这个布局的各个边距及间隔是默认的，加上这个边距和间隔就会超出屏幕总的宽高，我把主布局的边距及间隔设置成0，果然就能全屏显示了。因此当设置各方面大小时，无论是设置成确定值还是百分比一定要考虑是否超出屏幕范围，否则就会像我开始一样出现各种问题。


##三.移植到TX1##

 开始想通过交叉编译直接编译到TX1上，但是找了很久也没找到合适的方法，后来决定在TX1上也安装QT然后再重新编译一次。这是因为TX1是ARM编译器，与主机的编译器不同。
 
####1.TX1上安装QT####
安装教程如下：    
https://blog.csdn.net/u011475210/article/details/76392431    
需要注意的是，TX1配置过程一定要严格按照教程的步骤进行，否则配置kits会出现错误。
####2.TX1上编译运行####
直接将主机上的整个工程文件夹拷贝到TX1上，自己建立工程存放目录，然后在TX1上打开QT Creator根据路径打开保存好的工程，直接打开“.pro”文件即可。打开时会弹出一个窗口，意思大概是此工程使用的编译器与系统不符是否要选择TX1的编译器，直接确定即可。“Ctrl+B”编译成功后“Ctrl+R”运行即可。
















                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        