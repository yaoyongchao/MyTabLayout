# MyTabLayout
 TabLayout+ViewPager+Fragment实现底部导航
 很多Android的应用都有采用底部导航栏的功能，这样可以使得用户在使用过程中随意切换不同的页面，今天我分享一种方法：TabLayout+ViewPager+Fragment实现底部导航

编码准备：在项目的build.gradle添加依赖：compile 'com.android.support:design:23.1.1'

实现步骤：

第一,实现主布局文件:activity_main.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.sgcc.mytablayout.MainActivity">

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        ></android.support.v4.view.ViewPager>

    <android.support.design.widget.TabLayout
        android:id="@+id/tablayout"
        android:layout_width="match_parent"
        android:layout_height="@dimen/tab_height"
        style="@style/CustomTab"
        ></android.support.design.widget.TabLayout>



</LinearLayout>
使用TabLayout需添加 xmlns:app="http://schemas.android.com/apk/res-auto"


第二,TabLayout样式：CustomTab

<style name="CustomTab" parent="Widget.Design.TabLayout" >
    <item name="tabIndicatorColor">@color/tab_indicator</item>
    <item name="tabTextColor">@color/tab_tv_normal</item>
    <item name="tabSelectedTextColor">@color/tab_tv_selected</item>
    <item name="tabBackground">@drawable/bg_tab_border_selecter</item>
    <item name="tabIndicatorHeight">0dp</item>
</style>
<!--
1.tabIndicatorColor：TabLayout指示器的颜色
2.tabTextColor: tab默认字体的颜色
3.tabSelectedTextColor：当前选择的tab的字体颜色
4.tabBackground：背景
5.tabIndicatorHeight：TabLayout指示器高度
-->

Tab文字选择器：

<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_selected="true" android:color="@color/tab_tv_selected"></item>
    <item android:state_selected="false" android:color="@color/tab_tv_normal"></item>
</selector>

Tab图片选择器

<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_selected="true" android:drawable="@mipmap/tab_weixin_selected"></item>
    <item android:state_selected="false" android:drawable="@mipmap/tab_weixin_normal"></item>
</selector>

第三，自定义Tab布局：tab——custom.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    >

    <ImageView
        android:id="@+id/img_tab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        />

    <TextView
        android:id="@+id/tv_tab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:textColor="@drawable/tv_tab_selector"
        />

</LinearLayout>
第四，Activity代码：MainActivity.class

public class MainActivity extends AppCompatActivity {
    private TabLayout mTabLayout;
    //Tab 文字
    private final int[] TAB_TITLES = new int[]{R.string.weixin,R.string.contacts,R.string.find,R.string.me};
    //Tab 图片
    private final int[] TAB_IMGS = new int[]{R.drawable.tab_weixin_selector,R.drawable.tab_contacts_selector,R.drawable.tab_find_selector,R.drawable.tab_me_selector};
    //Fragment 数组
    private final Fragment[] TAB_FRAGMENTS = new Fragment[] {new WeiXinFragment(),new ContactsFragment(),new FindFragment(),new MeFragment()};
    //Tab 数目
    private final int COUNT = TAB_TITLES.length;
    private MyViewPagerAdapter mAdapter;
    private ViewPager mViewPager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initViews();
    }

    private void initViews() {
        mTabLayout = (TabLayout)findViewById(R.id.tablayout);
        setTabs(mTabLayout,this.getLayoutInflater(),TAB_TITLES,TAB_IMGS);

        mAdapter = new MyViewPagerAdapter(getSupportFragmentManager());
        mViewPager = (ViewPager) findViewById(R.id.viewpager);
        mViewPager.setAdapter(mAdapter);
        mViewPager.addOnPageChangeListener(new TabLayout.TabLayoutOnPageChangeListener(mTabLayout));
        mTabLayout.setOnTabSelectedListener(new TabLayout.ViewPagerOnTabSelectedListener(mViewPager));
    }

    /**
     * @description: 设置添加Tab
     */
    private void setTabs(TabLayout tabLayout,LayoutInflater inflater,int[] tabTitlees,int[] tabImgs){
        for (int i = 0; i < tabImgs.length; i++) {
            TabLayout.Tab tab = tabLayout.newTab();
            View view = inflater.inflate(R.layout.tab_custom,null);
            tab.setCustomView(view);

            TextView tvTitle = (TextView)view.findViewById(R.id.tv_tab);
            tvTitle.setText(tabTitlees[i]);
            ImageView imgTab = (ImageView) view.findViewById(R.id.img_tab);
            imgTab.setImageResource(tabImgs[i]);
            tabLayout.addTab(tab);

        }
    }

    /**
     * @description: ViewPager 适配器
     */
    private class MyViewPagerAdapter extends FragmentPagerAdapter {
        public MyViewPagerAdapter(FragmentManager fm) {
            super(fm);
        }

        @Override
        public Fragment getItem(int position) {
            return TAB_FRAGMENTS[position];
        }

        @Override
        public int getCount() {
            return COUNT;
        }
    }
}


至此一个底部导航栏的功能基本就实现了。
