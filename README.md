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
<img width="300" height="500" src="https://github.com/chenzifeng123/image/blob/master/Notepad001.PNG"/>

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

### （3）.功能介绍
1. 时间戳    
   在Cuns中添加时间属性，创建数据库  
   
        public void onCreate(SQLiteDatabase db) {
                db.execSQL("create table mybook(" +
                        "ids integer PRIMARY KEY autoincrement," +
                        "title text," +
                        "content text," +
                        "color text,"+
                        "times text)");
            }
  创建时显示的时间在NewNote的isSave函数中实现  
 
            SimpleDateFormat formatter = new SimpleDateFormat("yyyy.MM.dd  HH:mm:ss");
            Date curDate = new Date(System.currentTimeMillis());//获取当前时间
            String times = formatter.format(curDate);
            
  显示时间，在list_item.xml中用一个TextView来显示时间
  
        <TextView
            android:id="@+id/textView4"
            android:textSize="13sp"
            android:layout_width="wrap_content"
            android:layout_height="90dp"
            android:gravity="center"
            android:paddingRight="10dp"
            android:text="@string/time" />
            
  在MyAdapter中的getView函数中获取时间            
  
            vh.tv2.setText(list.get(position).getTimes());
   
   时间显示在相应的条目上
   效果图:  
   <img width="280" height="500" src="https://github.com/chenzifeng123/image/blob/master/Notepad002.PNG"/>
    
   
2. 笔记查询功能  
   SearchView搭配Toolbar实现查询功能.
   首先，新建一个menu_search.xml 
   
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

    在activity_main.xml中添加Toolbar  

       <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="?attr/colorPrimary"
            app:titleTextColor="@android:color/white" />
            
     在MyAdapter中实现Filterable接口  
     
          @Override
        public Filter getFilter() {
            // 如果MyFilter对象为空，那么重写创建一个
            if (filter == null) {
                filter = new MyFilter(list);
            }
            return filter;
        }
        /**
         * 创建内部类MyFilter继承Filter类，并重写相关方法，实现数据的过滤
         */
        class MyFilter extends Filter {
            // 创建集合保存原始数据
            private ArrayList<Cuns> original = new ArrayList<Cuns>();

            public MyFilter(ArrayList<Cuns> list) {
                this.original = list;
            }
            /**
             * 该方法返回搜索过滤后的数据
             */
            @Override
            protected FilterResults performFiltering(CharSequence constraint) {
                // 创建FilterResults对象
                FilterResults results = new FilterResults();

                /**
                 * 没有搜索内容的话就还是给results赋值原始数据的值和大小
                 * 执行了搜索的话，根据搜索的规则过滤即可，最后把过滤后的数据的值和大小赋值给results
                 *
                 */

                if(TextUtils.isEmpty(constraint)){
                    results.values = original;
                    results.count = original.size();
                }else {
                    // 创建集合保存过滤后的数据
                    ArrayList<Cuns> mList = new ArrayList<Cuns>();
                    // 遍历原始数据集合，根据搜索的规则过滤数据
                    for(Cuns s: original){
                        // 这里就是过滤规则的具体实现【规则有很多，大家可以自己决定怎么实现】
                        if(s.getTitle().trim().toLowerCase().contains(constraint.toString().trim().toLowerCase())){
                            // 规则匹配的话就往集合中添加该数据
                            mList.add(s);
                        }
                    }
                    results.values = mList;
                    results.count = mList.size();
                }
                // 返回FilterResults对象
                return results;
            }
            /**
             * 该方法用来刷新用户界面，根据过滤后的数据重新展示列表
             */
            @Override
            protected void publishResults(CharSequence constraint,
                                          FilterResults results) {
                // 获取过滤后的数据
                list = (ArrayList<Cuns>) results.values;
                // 如果接口对象不为空，那么调用接口中的方法获取过滤后的数据，具体的实现在new这个接口的时候重写的方法里执行
                if(listener != null){
                    listener.getFilterData(list);
                }
                // 刷新数据源显示
                notifyDataSetChanged();
            }
        }
        
 在MainActivity中的onCreateOptionsMenu函数中引用并设置文本监听
        
     //找到searchView并配置相关参数
            MenuItem searchItem = menu.findItem(R.id.action_search);
            mSearchView = (SearchView) MenuItemCompat.getActionView(searchItem);
            //搜索图标是否显示在搜索框内
            mSearchView.setIconifiedByDefault(true);
            //设置搜索框展开时是否显示提交按钮，可不显示
            mSearchView.setSubmitButtonEnabled(true);
            //让键盘的回车键设置成搜索
            mSearchView.setImeOptions(EditorInfo.IME_ACTION_SEARCH);
            //搜索框是否展开，false表示展开
            mSearchView.setIconified(false);
            //获取焦点
            mSearchView.setFocusable(true);
            mSearchView.requestFocusFromTouch();
            //设置提示词
            mSearchView.setQueryHint("请输入关键字");
            inflater=getLayoutInflater();
            mdb=new MyDataBase(this);
            array=mdb.getArray();  //得到ListView的数据，从数据库里查找后解析
            adapter = new MyAdapter(inflater,array, this, new FilterListener() {
                // 回调方法获取过滤后的数据
                public void getFilterData(ArrayList<Cuns> list) {
                    // 这里可以拿到过滤后数据，所以在这里可以对搜索后的数据进行操作
                    //  Log.e("TAG", "接口回调成功");
                   // Log.e("TAG", list.toString());
                }
            });
            lv.setAdapter(adapter);
            lv.setTextFilterEnabled(true);  //用来过滤选项
            //设置搜索文本监听
            mSearchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
                @Override
                public boolean onQueryTextSubmit(String query) {
                    //在文字改变的时候回调，query是改变之后的文字
                    //mSearchFragment.setSearchStr(query);
                    return false;
                }
                @Override
                public boolean onQueryTextChange(String newText) {
                    //文字提交的时候哦回调，newText是最后提交搜索的文字
                    if (!TextUtils.isEmpty(newText)){
                        adapter.getFilter().filter(newText);
                    }else{
                        adapter.getFilter().filter("");
                    }
                    return false;
                }
            });

效果图：  
<img width="280" height="500" src="https://github.com/chenzifeng123/image/blob/master/Notepad003.PNG"/>
<img width="280" height="500" src="https://github.com/chenzifeng123/image/blob/master/Notepad004.PNG"/>  

3. 更换背景  
   *  更换主界面背景  
      > 利用SharedPreferences进行设置的存储  
       在MainActivity中自定义get和set方法获取和更改文件中的值

           public void getBColor(){
                SharedPreferences sp=getSharedPreferences("data",MODE_PRIVATE);
                color=sp.getString("color",null);
                if(color==null) color="#efefef";
                    ll.setBackgroundColor(Color.parseColor(color));
            }
            public void setBColor(String color){
                SharedPreferences sp=getSharedPreferences("data",MODE_PRIVATE);
                SharedPreferences.Editor edit=sp.edit(); //获取编辑器
                edit.putString("color",color);
                edit.commit();
            }
       > 在onOptionsItemSelected函数中添加点击菜单项后改变背景颜色并将修改后的值传入文件中的操作
        
          //设置背景颜色
             case R.id.lv:
                 color="#009688";
                 setBColor(color);
                 ll.setBackgroundColor(Color.parseColor("#009688"));
                 break;
             case R.id.zi:
                 color="#673AB7";
                 setBColor(color);
                 ll.setBackgroundColor(Color.parseColor("#673AB7"));
                 break;
             case R.id.lan:
                 color="#2196F3";
                 setBColor(color);
                 ll.setBackgroundColor(Color.parseColor("#2196F3"));
                 break;
             case R.id.bai:
                 color="#efefef";
                 setBColor(color);
                 ll.setBackgroundColor(Color.parseColor("#FFFFFF"));
                 break;
  > 效果图:  
 <img width="280" height="500" src="https://github.com/chenzifeng123/image/blob/master/Notepad005.PNG"/>
 <img width="280" height="500" src="https://github.com/chenzifeng123/image/blob/master/Notepad006.PNG"/>
 
* 更换listitem背景  
   > 在Cuns中添加color属性并在建表时增加color列 根据每个listitem所存的color值 获得其背景颜色  
            
            private String color;   //背景颜色

             public void onCreate(SQLiteDatabase db) {
                     db.execSQL("create table mybook(" +
                             "ids integer PRIMARY KEY autoincrement," +
                             "title text," +
                             "content text," +
                             "color text,"+
                             "times text)");
                 }

    > 在MyAdapter的getView中设置背景颜色 

                @Override
                    public View getView(int position, View convertView, ViewGroup parent) {
                        ViewHolder vh;
                        String cl;
                        if(convertView==null){
                            vh=new ViewHolder();
                            convertView=inflater.inflate(R.layout.list_item, null);//注意导包，别导系统包
                            vh.tv1=(TextView) convertView.findViewById(R.id.textView3);
                            vh.tv2=(TextView) convertView.findViewById(R.id.textView4);
                            vh.linearLayoutProductNameList = convertView.findViewById(R.id.linearLayoutProductNameList);
                            convertView.setTag(vh);
                        }
                        vh=(ViewHolder) convertView.getTag();
                        vh.tv1.setText(list.get(position).getTitle());
                        vh.tv2.setText(list.get(position).getTimes());
                        //设置背景色
                        vh.linearLayoutProductNameList.setBackgroundColor(Color.parseColor(list.get(position).getColor()));
                        return convertView;
                    }







 



