# RecyclerView Pagination without Paging Library

I recently had a requirement where I had a use pagination in a ViewPager RecyclerView i.e. I needed to get list of transactions using a web service and then split those transactions by status (Success, Cancelled, Failed) and display them separately in tabs using ```TabLayout```. 
Initially, I thought about using [Paging Library](https://developer.android.com/topic/libraries/architecture/paging/) from the Android Jetpack series. </br>

#### Reason why Paging Library won't work in this scenario
But for this particular use case, Paging library would not work because there is <b>no way to manipulate the data from the Paging lib once it is fetched</b> from backend. Atleast there is no <i>strainghtforward</i> way to do this. A quick workaround would be to fetch the list of transactions from the web service, store them locally in [Room](https://developer.android.com/topic/libraries/architecture/room) and then query them based on status and update the UI. But this would not work because:</br> 
a) app was not allowed to store the transactions locally</br>
b) Even if I stored it locally, I needed to purge the data once the app was closed. There is a filter option which needed to again purge the existing data and fetch it fresh from the backend.


#### Paginator Solution
So my solution was to use the good old way of ```RecyclerView.OnScrollListener``` to paginate my data.

##### RecyclerViewPaginator.java
```
public abstract class RecyclerViewPaginator extends RecyclerView.OnScrollListener {

    /*
     * This is the Page Limit for each request
     * i.e. every request will fetch 19 transactions
     * */
    private Long batchSize = 19l;

    /*
     * Variable to keep track of the current page
     * */
    private Long currentPage = 0l;

    /*
     * This variable is used to set 
     * the threshold. For instance, if I have 
     * set the page limit to 20, this will notify 
     * the app to fetch more transactions when the 
     * user scrolls to the 18th item of the list.
     * */
    private Integer threshold = 2;

    /*
     * This is a hack to ensure that the app is notified 
     * only once to fetch more data. Since we use
     * scrollListener, there is a possibility that the 
     * app will be notified more than once when user is 
     * scrolling. This means there is a chance that the 
     * same data will be fetched from the backend again.
     * This variable is to ensure that this does NOT
     * happen.
     * */    
    private boolean endWithAuto = false;

    /*
     * We pass the RecyclerView to the constructor
     * of this class to get the LayoutManager
     * */
    private RecyclerView.LayoutManager layoutManager;
    public RecyclerViewPaginator(RecyclerView recyclerView) {
        recyclerView.addOnScrollListener(this);
        this.layoutManager = recyclerView.getLayoutManager();
    }

    @Override
    public void onScrollStateChanged(@NonNull RecyclerView recyclerView, int newState) {
        super.onScrollStateChanged(recyclerView, newState);
        if(newState == SCROLL_STATE_IDLE) {
            int visibleItemCount = layoutManager.getChildCount();
            int totalItemCount = layoutManager.getItemCount();

            int firstVisibleItemPosition = 0;
            if(layoutManager instanceof LinearLayoutManager) {
                firstVisibleItemPosition = ((LinearLayoutManager)layoutManager).findLastVisibleItemPosition();

            } else if(layoutManager instanceof GridLayoutManager) {
                firstVisibleItemPosition = ((GridLayoutManager)layoutManager).findLastVisibleItemPosition();
            }
            
            //if(isLoading()) return
            if(isLastPage()) return;

            if ((visibleItemCount + firstVisibleItemPosition + threshold) >= totalItemCount) {
                if(!endWithAuto) {
                    endWithAuto = true;
                    loadMore(getStartSize(), getMaxSize());
                }
            } else {
                endWithAuto = false;
            }
        }
    }

    public Long getStartSize() {
        return ++currentPage;
    }

    public Long getMaxSize() {
        return  currentPage + batchSize;
    }


    /*
     * I have added a reset method here
     * that can be called from the UI because
     * if we have a filter option in the app,
     * we might need to refresh the whole data set
     * */    
    public void reset() {
        currentPage = 0l;
    }

    @Override
    public void onScrolled(@NonNull RecyclerView recyclerView, int dx, int dy) {
        super.onScrolled(recyclerView, dx, dy);
    }


    /*
     * I have created two abstract methods:
     * isLastPage() where the UI can specify if 
     * this is the last page - this data usually comes from the backend.
     * 
     * loadMore() where the UI can specify to load
     * more data when this method is called.
     * 
     * We can also specify another method called
     * isLoading() - to let the UI display a loading View.
     * Since I did not need to display this, I have 
     * commented it out. 
     * */    
    //public abstract boolean isLoading();
    
    public abstract boolean isLastPage();
    public abstract void loadMore(Long start , Long count);
}
```


##### RecyclerViewPaginator.kt
```
abstract class RecyclerViewPaginator(recyclerView: RecyclerView) : RecyclerView.OnScrollListener() {

    /*
     * This is the Page Limit for each request
     * i.e. every request will fetch 19 transactions
     * */
    private val batchSize = 19L

    /*
     * Variable to keep track of the current page
     * */
    private var currentPage: Long = 0L

    /*
     * This variable is used to set
     * the threshold. For instance, if I have
     * set the page limit to 20, this will notify
     * the app to fetch more transactions when the
     * user scrolls to the 18th item of the list.
     * */
    private val threshold = 2

    /*
     * This is a hack to ensure that the app is notified
     * only once to fetch more data. Since we use
     * scrollListener, there is a possibility that the
     * app will be notified more than once when user is
     * scrolling. This means there is a chance that the
     * same data will be fetched from the backend again.
     * This variable is to ensure that this does NOT
     * happen.
     * */
    private var endWithAuto = false

    /*
     * We pass the RecyclerView to the constructor
     * of this class to get the LayoutManager
     * */
    private val layoutManager: RecyclerView.LayoutManager?

    val startSize: Long
        get() = ++currentPage

    val maxSize: Long
        get() = currentPage + batchSize


    /*
     * I have created two abstract methods:
     * isLastPage() where the UI can specify if
     * this is the last page - this data usually comes from the backend.
     *
     * loadMore() where the UI can specify to load
     * more data when this method is called.
     *
     * We can also specify another method called
     * isLoading() - to let the UI display a loading View.
     * Since I did not need to display this, I have
     * commented it out.
     * */
    //public abstract boolean isLoading();

    abstract val isLastPage: Boolean

    init {
        recyclerView.addOnScrollListener(this)
        this.layoutManager = recyclerView.layoutManager
    }

    override fun onScrollStateChanged(recyclerView: RecyclerView, newState: Int) {
        super.onScrollStateChanged(recyclerView, newState)
        if (newState == SCROLL_STATE_IDLE) {
            val visibleItemCount = layoutManager!!.childCount
            val totalItemCount = layoutManager.itemCount

            var firstVisibleItemPosition = 0
            if (layoutManager is LinearLayoutManager) {
                firstVisibleItemPosition = layoutManager.findLastVisibleItemPosition()

            } else if (layoutManager is GridLayoutManager) {
                firstVisibleItemPosition = layoutManager.findLastVisibleItemPosition()
            }

            //if(isLoading()) return
            if (isLastPage) return

            if (visibleItemCount + firstVisibleItemPosition + threshold >= totalItemCount) {
                if (!endWithAuto) {
                    endWithAuto = true
                    loadMore(startSize, maxSize)
                }
            } else {
                endWithAuto = false
            }
        }
    }


    /*
     * I have added a reset method here
     * that can be called from the UI because
     * if we have a filter option in the app,
     * we might need to refresh the whole data set
     * */
    fun reset() {
        currentPage = 0L
    }

    override fun onScrolled(recyclerView: RecyclerView, dx: Int, dy: Int) {
        super.onScrolled(recyclerView, dx, dy)
    }

    abstract fun loadMore(start: Long, count: Long)
}
```

<b>Usage in Java</b>
```
RecyclerViewPaginator recyclerViewPaginator = new RecyclerViewPaginator(recyclerView) {
       @Override
       public boolean isLastPage() {
            return viewModel.isLastPage();
       }

        @Override
        public void loadMore(Long start, Long count) {
            viewModel.loadData(start, count);
        }
 };

    /* Add this paginator to the onScrollListener of RecyclerView  */    
    recyclerView.addOnScrollListener(recyclerViewPaginator);
```

<b>Usage in Kotlin:</b>
```
recyclerView.addOnScrollListener(object : RecyclerViewPaginator(recyclerView) {
            override val isLastPage: Boolean
                get() = viewModel.isLastPage()

            override fun loadMore(page: Long) {
                      viewModel.loadData(page)
            }
        })
```

That's it!
If you would like to know how the Google Paging Library works, please checkout this [link](https://proandroiddev.com/8-steps-to-implement-paging-library-in-android-d02500f7fffe). 
