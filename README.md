# Read ME
## 一. 实验名称
NOTEPAD笔记本应用
## 二. 实验要求与目的
-基本要求  
1. NoteList中显示条目增加时间戳显示  
2. 添加笔记查询功能（根据标题查询）  

+附加功能  
1. UI美化  
2. 更改记事本的背景,更改显示条目的颜色 
3. 笔记分类：根据显示条目的颜色进行分类
4. 分享功能
 

## 三. 实验步骤
### （1）.程序大致理解  
程序组成结构如下图：  
![Image text](https://github.com/chenzifeng123/image/blob/master/Notepad001.PNG)   

### （2）.程序具体内容
1. Class  
* Activity部分
    * MainActivity:  
      >这个类主要包括7个点击事件，分别为：  
      * ListView的长按点击事件，用来AlertDialog来判断是否删除数据。  
      * ListView的点击事件，跳转到第二个界面，用来修改数据。  
      * 新建便签按钮的点击事件，跳转到第二界面，用来新建便签。  
      * menu里的退出事件，用来退出程序。    
      * menu里的新建事件，用来新建便签。   
      * menu里的更换背景事件，用来更换背景。      
      * menu里的分类事件，用来根据颜色对listitem进行筛选。  
      >导航栏中的搜索框：
      * 用于根据标题进行笔记查询。
    *  NewNote: 新建界面 Activity
  
* 数据库部分  
   * Cuns:存放数据的类  
   * MyDataBase:用来操作数据库
   * MyAdapter:自定义 Adapter，
   * MyOpenHelper:重写SQLiteOpenHelper类，用来建立数据库和表
* 其他  
   * DragFloatActionButton:自定义悬浮按钮 
   * FilterListener:接口类，抽象方法用来获取过滤后的数据
 
2. 布局
* activity_main:主界面布局，是一个线性布局，其中包含Toolbar控件和一个相对布局，相对布局中又包含一个悬浮按钮和一个ListView  

        <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context=".MainActivity"
        >

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:background="@color/color_green"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:title="@string/app_name"
            app:titleTextColor="@android:color/white"
            />

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#efefef"
            android:id="@+id/ll_main"
            android:paddingBottom="5dp"
            android:paddingTop="5dp"
           >

            <ListView
                android:paddingLeft="13dp"
                android:paddingRight="13dp"
                android:id="@+id/lv_bwlList"
                android:divider="#00000000"
                android:dividerHeight="9dp"
                android:layout_width="match_parent"
                android:layout_height="match_parent"/>

            <com.example.notepad2.DragFloatActionButton
                android:id="@+id/fb"
                android:layout_height="wrap_content"
                android:layout_width="wrap_content"
                app:borderWidth="0dp"
                app:backgroundTint="#FFFFFF"
                app:rippleColor="#008577"
                android:src="@drawable/hao"
                android:layout_alignParentRight="true"
                android:layout_centerVertical="true"
                />

        </RelativeLayout>

        </LinearLayout>  

* activity_new_note:新建界面布局，是一个线性布局，其中包含一个线性布局和一个ImageButton，线性布局中又包含两个TextView和两个EditText 
* list_item:NoteList中用来显示每一条笔记的布局的布局文件，是一个线性布局，其中包含两个TextView
* main: 主界面菜单栏，包含新建，退出，更换背景，分类几个选项
* menu_search: 主界面导航栏中的搜索框  

        <?xml version="1.0" encoding="utf-8"?>
        <menu xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto">

            <item
                android:id="@+id/action_search"
                android:icon="@drawable/ic_launcher_foreground"
                android:title="search"
                app:actionViewClass="android.support.v7.widget.SearchView"
                app:showAsAction="ifRoom" />

            <item
                android:id="@+id/action_settings"
                android:orderInCategory="100"
                android:title="action_settings"
                app:showAsAction="never"/>
        </menu>

* second:新建界面菜单栏，包含背景颜色，分享几个选项

 



