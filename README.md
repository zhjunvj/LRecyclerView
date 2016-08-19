# LRecyclerView 

LRecyclerView是支持addHeaderView、 addFooterView、下拉刷新、分页加载数据的RecyclerView。

新增功能：SwipeMenu系列功能，包括Item侧滑菜单、长按拖拽Item，滑动删除Item等。

**它对 RecyclerView 控件进行了拓展，给RecyclerView增加HeaderView、FooterView，并且不需要对你的Adapter做任何修改。**


##效果图
![这里写图片描述](https://raw.githubusercontent.com/cundong/HeaderAndFooterRecyclerView/master/art/art1.png)

##Gradle
--

Step 1. 在你的根build.gradle文件中增加JitPack仓库依赖。

```
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
    }
}
```	

Step 2. 在你的model的build.gradle文件中增加LRecyclerView依赖。
```
compile 'com.github.jdsjlzx:LRecyclerView:1.0.6'
```

LRecyclerView requires at minimum Java 7 or Android 4.0.


##项目简述
1. 下拉刷新、滑动到底部自动加载下页数据； 
2. 可以方便添加Header和Footer；
3. 头部下拉样式可以自定义；
4. 具备item点击和长按事件；
5. 网络错误加载失败点击Footer重新请求数据；
6. 可以动态为FooterView赋予不同状态（加载中、加载失败、滑到最底等）；
7. SwipeMenu系列功能，包括Item侧滑菜单、长按拖拽Item，滑动删除Item等；
8. 实现viewpager的功能。

<br>注意：EndlessLinearLayoutActivity.java类里面有标准完整的使用方法，请尽量在这个界面看效果。</b>



##Demo下载
[点我下载](https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/app/app-release.apk)

##功能介绍


### 填充数据

```
mDataAdapter = new DataAdapter(this);
mDataAdapter.setData(dataList);

mLRecyclerViewAdapter = new LRecyclerViewAdapter(this, mDataAdapter);
mRecyclerView.setAdapter(mLRecyclerViewAdapter);
```

> 
1. DataAdapter是用户自己真正的adapter，用户自己定义；
2. LRecyclerViewAdapter提供了一些实用的功能，使用者不用关心它的实现，只需构造的时候把自己的mDataAdapter以参数形式传进去即可。

### 添加HeaderView、FooterView
```
//add a HeaderView
RecyclerViewUtils.setHeaderView(mRecyclerView, new SampleHeader(this));

//add a FooterView
RecyclerViewUtils.setFooterView(mRecyclerView, new SampleFooter(this));
```
添加HeaderView还可以使用下面两种方式：

```
View header = LayoutInflater.from(this).inflate(R.layout.sample_header,(ViewGroup)findViewById(android.R.id.content), false);
RecyclerViewUtils.setHeaderView(mRecyclerView, header);


CommonHeader headerView = new CommonHeader(getActivity(), R.layout.layout_home_header);
RecyclerViewUtils.setHeaderView(mRecyclerView, headerView);
```

上面的方式同样适用于FooterView。

### LScrollListener-滑动监听事件接口

LScrollListener实现了nRefresh()、onScrollUp()、onScrollDown()、onBottom()、onScrolled五个事件，如下所示：

```
void onRefresh();//pull down to refresh

void onScrollUp();//scroll down to up

void onScrollDown();//scroll from up to down

void onBottom();//load next page

void onScrolled(int distanceX, int distanceY);// moving state,you can get the move distance
```
 - onRefresh()——RecyclerView下拉刷新事件；
 - onScrollUp()——RecyclerView向上滑动的监听事件；
 - onScrollDown()——RecyclerView向下滑动的监听事件；
 - onBottom()——RecyclerView滑动到底部的监听事件；
 - onScrollDown()——RecyclerView正在滚动的监听事件；
 
使用：
```
mRecyclerView.setLScrollListener(new LRecyclerView.LScrollListener() {
            @Override
            public void onRefresh() {
                //下拉刷新
            }

            @Override
            public void onScrollUp() {
            }

            @Override
            public void onScrollDown() {
            }

            @Override
            public void onBottom() {
                // 加载更多（加载下页数据）
            }

            @Override
            public void onScrolled(int distanceX, int distanceY) {
            }

        });
 
```

###下拉刷新和加载更多

详见LScrollListener接口介绍。

####设置下拉刷新样式

```
mRecyclerView.setRefreshProgressStyle(ProgressStyle.BallSpinFadeLoader); //设置下拉刷新Progress的样式
mRecyclerView.setArrowImageView(R.drawable.iconfont_downgrey);  //设置下拉刷新箭头
```

AVLoadingIndicatorView库有多少效果，LRecyclerView就支持多少下拉刷新效果，当然你也可以自定义下拉刷新效果。

效果图：

![这里写图片描述](http://img.blog.csdn.net/20160701173404897)

####设置加载更多样式

拷贝LRecyclerview_library中的文件文件layout_recyclerview_list_footer_loading.xml到你的工程中，修改后即可。


###开启和禁止下拉刷新功能

```
mRecyclerView.setPullRefreshEnabled(true);
```

or
```
mRecyclerView.setPullRefreshEnabled(false);
```

默认是开启。


###强制刷新

根据大家的反馈，增加了一个强制刷新的方法，使用如下：

```
mRecyclerView.forceToRefresh();
```

**无论是下拉刷新还是强制刷新，刷新完成后调用下面代码：**

```
mRecyclerView.refreshComplete();
mLRecyclerViewAdapter.notifyDataSetChanged();
```

###下拉刷新清空数据
有的时候，需要下拉的时候情况数据并更新UI，可以这么做：
```
@Override
public void onRefresh() {
    RecyclerViewStateUtils.setFooterViewState(mRecyclerView,LoadingFooter.State.Normal);
    mDataAdapter.clear();
    mCurrentCounter = 0;
    isRefresh = true;
    requestData();
}
```
如果不需要下拉的时候情况数据并更新UI，如下即可：
```
@Override
public void onRefresh() {
    isRefresh = true;
    requestData();
}
```

### 加载网络异常处理
--------
加载数据时如果网络异常或者断网，LRecyclerView为你提供了重新加载的机制。

效果图：

![这里写图片描述](https://raw.githubusercontent.com/cundong/HeaderAndFooterRecyclerView/master/art/art5.png)

网络异常出错代码处理如下：

```
RecyclerViewStateUtils.setFooterViewState(getActivity(), mRecyclerView, getPageSize(), LoadingFooter.State.NetWorkError, mFooterClick);

private View.OnClickListener mFooterClick = new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            RecyclerViewStateUtils.setFooterViewState(getActivity(), mRecyclerView, getPageSize(), LoadingFooter.State.Loading, null);
            requestData();
        }
    };
```

上面的mFooterClick就是我们点击底部的Footer时的逻辑处理事件，很显然我们还是在这里做重新请求数据操作。


### 点击事件和长按事件处理

先看下怎么使用：

```
mLRecyclerViewAdapter.setOnItemClickLitener(new OnItemClickLitener() {
            @Override
            public void onItemClick(View view, int position) {
                ItemModel item = mDataAdapter.getDataList().get(position);
                Toast.makeText(EndlessLinearLayoutActivity.this, item.title, Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onItemLongClick(View view, int position) {
                ItemModel item = mDataAdapter.getDataList().get(position);
                Toast.makeText(EndlessLinearLayoutActivity.this, "onItemLongClick - " + item.title, Toast.LENGTH_SHORT).show();
            }
        });
```

原理就是实现viewHolder.itemView的点击和长按事件。由于代码过多就不贴出来了。

viewHolder源码如下：

```
public static abstract class ViewHolder {
        public final View itemView;
        int mPosition = NO_POSITION;
        int mOldPosition = NO_POSITION;
        long mItemId = NO_ID;
        int mItemViewType = INVALID_TYPE;
        int mPreLayoutPosition = NO_POSITION;
```

###设置空白View（setEmptyView）

```
mRecyclerView.setEmptyView(view);
```

###SwipeMenuAdapter

为了实现SwipeMenu的功能，此次新增了一个[SwipeMenuAdapter](https://github.com/jdsjlzx/LRecyclerView/blob/master/LRecyclerview_library/src/main/java/com/github/jdsjlzx/swipe/SwipeMenuAdapter.java)类。

SwipeMenuAdapter与library中已经存在的LRecyclerViewAdapter会不会冲突呢？答案是不会。SwipeMenuAdapter是用户级别的基类adapter，也就是用户需要继承SwipeMenuAdapter去实现自己的adapter，具体使用同以前。

SwipeMenuAdapter类的定义：
```
public abstract class SwipeMenuAdapter<VH extends RecyclerView.ViewHolder> extends RecyclerView.Adapter<VH> 
```

实现自己的MenuAdapter：

```
public class MenuAdapter extends SwipeMenuAdapter<MenuAdapter.DefaultViewHolder> {

    protected List<ItemModel> mDataList = new ArrayList<>();

    public MenuAdapter() {
    }

    @Override
    public int getItemCount() {
        return mDataList.size();
    }

    @Override
    public View onCreateContentView(ViewGroup parent, int viewType) {
        return LayoutInflater.from(parent.getContext()).inflate(R.layout.list_item_text_swipe, parent, false);
    }

    @Override
    public MenuAdapter.DefaultViewHolder onCompatCreateViewHolder(View realContentView, int viewType) {
        return new DefaultViewHolder(realContentView);
    }

    @Override
    public void onBindViewHolder(MenuAdapter.DefaultViewHolder holder, int position) {

        String item = mDataList.get(position).title;

        DefaultViewHolder viewHolder = holder;
        viewHolder.tvTitle.setText(item);
    }

    static class DefaultViewHolder extends RecyclerView.ViewHolder {
        TextView tvTitle;

        public DefaultViewHolder(View itemView) {
            super(itemView);
            tvTitle = (TextView) itemView.findViewById(R.id.tv_title);
        }

    }

}
```

是不是很方便？MenuAdapter基本的功能都满足了，直接拷贝到项目中即可使用。

###左右两侧都有菜单

效果图：

<img src="https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/art/swipe_1.gif" width=320 height=560 />

具体使用步骤如下。

- 为SwipeRecyclerView的Item创建菜单
```
// 设置菜单创建器。
mRecyclerView.setSwipeMenuCreator(swipeMenuCreator);
//设置菜单Item点击监听事件        
mRecyclerView.setSwipeMenuItemClickListener(menuItemClickListener);
```

swipeMenuCreator完成了左右菜单的创建，menuItemClickListener实现了菜单的点击事件。

需要注意的是，LRecyclerView提供了下面两个方法，具体使用请详见demo。

```
public void openLeftMenu(int position, int duration) {
        openMenu(position, LEFT_DIRECTION, duration);
    }

    public void openRightMenu(int position) {
        openMenu(position, RIGHT_DIRECTION, SwipeMenuLayout.DEFAULT_SCROLLER_DURATION);
    }
```
openLeftMenu：打开item的左边菜单
openRightMenu：打开item的右边菜单

这里关键的就是这个position（详细请参考demo）。


###根据ViewType显示菜单

效果图：

<img src="https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/art/swipe_2.gif" width=320 height=560 />

根据ViewType决定SwipeMenu在哪一行出现，可以左侧，可以右侧。

自定义MenuViewTypeAdapter，代码如下：
```
public class MenuViewTypeAdapter extends MenuAdapter {

    public static final int VIEW_TYPE_MENU = 1;
    public static final int VIEW_TYPE_NONE = 2;

    @Override
    public int getItemViewType(int position) {
        return position % 2 == 0 ? VIEW_TYPE_MENU : VIEW_TYPE_NONE;
    }
}

```

在实现swipeMenuCreator 时，需要根据ItemViewType值来决定是否创建左右菜单。

```
    private SwipeMenuCreator swipeMenuCreator = new SwipeMenuCreator() {
        @Override
        public void onCreateMenu(SwipeMenu swipeLeftMenu, SwipeMenu swipeRightMenu, int viewType) {
        // 根据Adapter的ViewType来决定菜单的样式、颜色等属性、或者是否添加菜单。
            if (viewType == MenuViewTypeAdapter.VIEW_TYPE_NONE) {
            
                // Do nothing.
            } else if (viewType == MenuViewTypeAdapter.VIEW_TYPE_MENU) {
                int size = getResources().getDimensionPixelSize(R.dimen.item_height);

                ......
            }
        }
    };
```

###长按拖拽Item(List)，与菜单结合

效果图：

<img src="https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/art/swipe_3.gif" width=320 height=560 />

关键代码：
```
mRecyclerView.setLongPressDragEnabled(true);// 开启拖拽功能        
mRecyclerView.setOnItemMoveListener(onItemMoveListener);// 监听拖拽，更新UI。
```

onItemMoveListener具体如下：
```
    /**
     * 当Item移动的时候。
     */
    private OnItemMoveListener onItemMoveListener = new OnItemMoveListener() {
        @Override
        public boolean onItemMove(int fromPosition, int toPosition) {
            final int adjFromPosition = mLRecyclerViewAdapter.getAdapterPosition(true, fromPosition);
            final int adjToPosition = mLRecyclerViewAdapter.getAdapterPosition(true, toPosition);
            // 当Item被拖拽的时候。
            Collections.swap(mDataAdapter.getDataList(), adjFromPosition, adjToPosition);
            //Be carefull in here!
            mLRecyclerViewAdapter.notifyItemMoved(fromPosition, toPosition);
            return true;// 返回true表示处理了，返回false表示你没有处理。
        }

        @Override
        public void onItemDismiss(int position) {
            // 当Item被滑动删除掉的时候，在这里是无效的，因为这里没有启用这个功能。
            // 使用Menu时就不用使用这个侧滑删除啦，两个是冲突的。
        }
    };
```

注意下面代码：

```
final int adjFromPosition = mLRecyclerViewAdapter.getAdapterPosition(true, fromPosition);
final int adjToPosition = mLRecyclerViewAdapter.getAdapterPosition(true, toPosition);
```

<font color="red">关于position的位置，为了大家使用方便，特在LRecyclerViewAdapter中提供了一个方法getAdapterPosition(boolean isCallback, int position)。</font>
>
>- isCallback 含义：position是否接口回调中带来的
> - position 含义：如果不是接口回调，就是用户自己指定的position
> - getAdapterPosition(boolean isCallback, int position)只用于非LRecyclerViewAdapter提供的接口。

举例说明：

- setOnItemMoveListener不是 LRecyclerViewAdapter自带接口（也就是内部方法），需要调用getAdapterPosition方法获得正确的position
- 如setOnItemClickLitener 是 LRecyclerViewAdapter自带接口，接口里面自带了position，用户就不必调用getAdapterPosition方法，直接使用就可以了。
```
mLRecyclerViewAdapter.setOnItemClickLitener(new OnItemClickLitener() {
            @Override
            public void onItemClick(View view, int position) {
                String text = "Click position = " + position;
               
            }

            @Override
            public void onItemLongClick(View view, int position) {


            }
        });
```

###长按拖拽Item(Grid)

效果图：

<img src="https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/art/swipe_4.gif" width=320 height=560 />

与list功能一样，只是布局不一样。

###滑动直接删除Item

效果图：

<img src="https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/art/swipe_5.gif" width=320 height=560 />

注意：
> 滑动删除和滑动菜单是互相冲突的，两者只能出现一个。

关键代码：

```
mRecyclerView.setLongPressDragEnabled(true);
mRecyclerView.setItemViewSwipeEnabled(true);// 开启滑动删除        mRecyclerView.setOnItemMoveListener(onItemMoveListener);// 监听拖拽，更新UI
```

按照配置就可以实现滑动删除。

###指定某个Item不能拖拽或者不能滑动删除

效果图：

<img src="https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/art/swipe_6.gif" width=320 height=560 />

关键代码：

```
mRecyclerView.setLongPressDragEnabled(true);
mRecyclerView.setItemViewSwipeEnabled(true);// 开启滑动删除。        mRecyclerView.setOnItemMoveListener(onItemMoveListener);// 监听拖拽，更新UI。        mRecyclerView.setOnItemMovementListener(onItemMovementListener);
```

###用SwipeMenuLayout实现你自己的侧滑

效果图：

<img src="https://raw.githubusercontent.com/jdsjlzx/LRecyclerView/master/art/swipe_7.gif" width=320 height=560 />

这个与LRecyclerView关系不大，但是与SwipeMenu关系密切。为了实现滑动菜单的功能，定义了SwipeMenuLayout，详细使用见demo。



##分组

效果图：

![这里写图片描述](https://github.com/jdsjlzx/LRecyclerView/blob/master/art/art6.gif)

功能还在完善中....




##代码混淆

```
#LRecyclerview_library
-dontwarn com.github.jdsjlzx.**
-keep class com.github.jdsjlzx.**{*;}
```

如果你想了解更多混淆配置，参考：http://blog.csdn.net/jdsjlzx/article/details/51861460

##注意事项

1. 如果添加了footerview，不要再使用setLScrollListener方法，如有需要，自定义实现即可。如下面代码不要同时使用：
```
mRecyclerView.setLScrollListener(LScrollListener); 
RecyclerViewUtils.setFooterView(mRecyclerView, new SampleFooter(this));
```
2. 不要SwipeRefreshLayout与LRecyclerView一起使用，会有冲突。

##Thanks

1.[HeaderAndFooterRecyclerView](https://github.com/cundong/HeaderAndFooterRecyclerView)

2.[SwipeRecyclerView](https://github.com/yanzhenjie/SwipeRecyclerView)


##打赏

觉得本框架对你有帮助，不妨打赏赞助我一下，让我有动力走的更远。

<img src="http://img.blog.csdn.net/20160812223409875" width=280 height=280 />

