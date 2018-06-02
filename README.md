
    private ViewPager vpArticle;
    private MyPagerAdapter myAdapter;
    private List<View> mListViews;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);


        myAdapter = new MyPagerAdapter();
        vpArticle = (ViewPager) findViewById(R.id.viewpager);

        mListViews = new ArrayList<View>();
        addView(mListViews, "https://www.baidu.com");
        addView(mListViews, "https://123.sogou.com/");
        addView(mListViews, "http://lcoc.top/2.2/");

        vpArticle.setAdapter(myAdapter);
    }

    private void addView(List<View> viewList,String url) {
        WebView webView=new WebView(this);
        WebSettings webSettings=webView.getSettings();

    ///JS处理
        webSettings.setJavaScriptEnabled(true);   //支持JS插件
        // 若加载的 html 里有JS 在执行动画等操作，会造成资源浪费（CPU、电量）
        // 在 onStop 和 onResume 里分别把 setJavaScriptEnabled() 给设置成 false 和 true 即可

        webSettings.setPluginState(WebSettings.PluginState.ON);  //设置是否支持flash插件
        webSettings.setJavaScriptCanOpenWindowsAutomatically(true);   //支持通过JS打开窗口



    ///缩放处理
        //设置自适应屏幕，两者合用
        webSettings.setUseWideViewPort(true);  //将图片调整到合适webview的大小
        webSettings.setLoadWithOverviewMode(true);  //缩放至屏幕的大小


        webSettings.setSupportZoom(true);      //支持缩放，默认为true，与BuiltInZoomControls一起用
        webSettings.setBuiltInZoomControls(true);   //设置内置的缩放控件，这个取决于setSupportZoom(),
        // 若setSupportZoom(false)，则该WebView不可缩放，这个不管设置什么都不能缩放。
        webSettings.setDisplayZoomControls(false);  //隐藏原生的缩放控件

    ///内容布局
        webSettings.setLayoutAlgorithm(WebSettings.LayoutAlgorithm.SINGLE_COLUMN); //支持内容重新布局
        webSettings.setSupportMultipleWindows(true);  //多窗口

    //文件缓存
        webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);  //关闭webview中缓存
        webSettings.setAllowFileAccess(true);   //设置可以访问文件
        webSettings.setDomStorageEnabled(true); //开启DOM storage API功能

        //离线加载
        webSettings.setDatabaseEnabled(true);   //开启 database storage API 功能
        webSettings.setAppCacheEnabled(true);   //开启 Application Caches 功能
        webSettings.setAppCachePath("path"); //设置  Application Caches 缓存目录
        /**
         * 每个 Application 只调用一次 WebSettings.setAppCachePath()，WebSettings.setAppCacheMaxSize()
         */


        ///其他设置
        webSettings.setNeedInitialFocus(true);  //当webview调用requestFocus时为webview设置节点
        webSettings.setLoadsImagesAutomatically(true);  //支持自动加载图片
        webSettings.setDefaultTextEncodingName("utf-8");  //设置编码格式
        webSettings.setDefaultFontSize(20); // 设置默认字体的大小

        webView.loadUrl(url);

        viewList.add(webView);




    }

    private class MyPagerAdapter extends PagerAdapter{
        @Override
        public int getCount() { return mListViews.size(); }

        @Override
        public boolean isViewFromObject(View view, Object object) {  return view==object;  }

        @Override
        public void destroyItem(ViewGroup container, int position, Object object) {
            ((ViewPager)container).removeView(mListViews.get(position));
        }

        @Override
        public Object instantiateItem(ViewGroup container, int position) {
            ((ViewPager)container).addView(mListViews.get(position),0);
            return mListViews.get(position);
        }
    }


    /**
     * 方式1. 加载一个网页：  webView.loadUrl("http://www.google.com/");
     * 方式2：加载apk包中的html页面：  webView.loadUrl("file:///android_asset/test.html");
     * 方式3：加载手机本地的html页面：  webView.loadUrl("content://com.android.htmlfileprovider/sdcard/test.html");
     * 方式4：加载 HTML 页面的一小段内容
     *  WebView.loadData(String data, String mimeType, String encoding)
     *    参数1：需要截取展示的内容，内容里不能出现 ’#’, ‘%’, ‘\’ , ‘?’ 这四个字符，若出现了需用 %23, %25, %27, %3f 对应来替代，否则会出现异常
     *    参数2：展示内容的类型
     *    参数3：字节码
     */



    /**
     WebView的状态
        激活WebView为活跃状态，能正常执行网页的响应
        webView.onResume();
         当页面被失去焦点被切换到后台不可见状态，需要执行onPause
         通过onPause动作通知内核暂停所有的动作，比如DOM的解析、plugin的执行、JavaScript执行。
         webView.onPause()；
         当应用程序(存在webview)被切换到后台时，这个方法不仅仅针对当前的webview而是全局的全应用程序的webview
         它会暂停所有webview的layout，parsing，javascripttimer。降低CPU功耗。
         webView.pauseTimers()
         恢复pauseTimers状态
         webView.resumeTimers();
         销毁Webview
         在关闭了Activity时，如果Webview的音乐或视频，还在播放。就必须销毁Webview
         但是注意：webview调用destory时,webview仍绑定在Activity上
         这是由于自定义webview构建时传入了该Activity的context对象
         因此需要先从父容器中移除webview,然后再销毁webview:
         rootLayout.removeView(webView);
         webView.destroy();
     */



    /**
        WebView LifeCycle
        onResume()：WebView为活跃状态时回调，可以正常执行网页的响应。
        onPause()： WebView被切换到后台时回调, 页面被失去焦点, 变成不可见状态，
                        onPause动作通知内核暂停所有的动作，比如DOM的解析、plugin的执行、JavaScript执行。
        pauseTimers()：  当应用程序被切换到后台时回调，该方法针对全应用程序的WebView，
                        它会暂停所有webview的layout，parsing，javascripttimer。降低CPU功耗。
        resumeTimers()： 恢复pauseTimers时的动作。
        destroy()：关闭了Activity时回调, WebView调用destory时, WebView仍绑定在Activity上.
                这是由于自定义WebView构建时传入了该Activity的context对象, 因此需要先从父容器中移除WebView, 然后再销毁webview。
        mRootLayout.removeView(webView);
        mWebView.destroy()
     */



    /**
         WebView: 主要负责解析和渲染网页
         WebViewClient: 辅助WebView处理各种通知和请求事件
         WebChromeClient: 辅助WebView处理JavaScript中的对话框, 网址图标和标题等
     */



    /**
    Android的返回键,
     是否可以后退
     Webview.canGoBack()
     后退网页
     Webview.goBack()
     是否可以前进
     Webview.canGoForward()
     前进网页
     Webview.goForward()
     以当前的index为起始点前进或者后退到历史记录中指定的steps
     如果steps为负数则为后退，正数则为前进
     Webview.goBackOrForward(intsteps)
     如果想要实现WebView内网页的回退, 可以重写onKeyEvent()方法。
         if ((keyCode == KeyEvent.KEYCODE_BACK) && myWebView.canGoBack()) {
            myWebView.goBack();
            return true;
         }
     */



    /**
     * 做下拉刷新等功能时, 经常会去判断WebView是否滚动到顶部或者滚动到底部
     *
     * getScrollY();  返回的是当前可见区域的顶端距整个页面顶端的距离,也就是当前内容滚动的距离.
     *
     * getHeight(); getBottom();  返回当前WebView这个容器的高度
     *
     * getContentHeight();  返回的是整个html的高度, 但并不等同于当前整个页面的高度,
     * 因为WebView有缩放功能, 所以当前整个页面的高度实际上应该是原始html的高度再乘上缩放比例
     * if (webView.getContentHeight() * webView.getScale() == (webView.getHeight() + webView.getScrollY())) {
     *    //已经处于底端
     *  }
     *
     * if(webView.getScrollY() == 0){  //处于顶端  }
     *
     * 以上这个方法常用,从API 17开始, mWebView.getScale()被标记为deprecated
     *
     */




    /**
     WebView缓存实现
        在项目中如果使用到WebView控件, 当加载html页面时, 会在/data/data/包名目录下生成database与cache两个文件夹。
        请求的url记录是保存在WebViewCache.db, 而url的内容是保存在WebViewCache文件夹下。
         控制缓存行为
            //优先使用缓存
            webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);  只要本地有，无论是否过期，或者no-cache，都使用缓存中的数据。
            webSettings.setCacheMode(LOAD_DEFAULT) （默认）根据cache-control决定是否从网络上取数据。
            webSettings.setCacheMode(WebSettings.LOAD_CACHE_ONLY);  不使用网络，只读取本地缓存数据
            WwebSettings.setCacheMode(WebSettings.LOAD_NO_CACHE);   不使用缓存，只从网络获取数据.
         清除缓存
             clearCache(true); //清除网页访问留下的缓存，由于内核缓存是全局的因此这个方法不仅仅针对webview而是针对整个应用程序.
             clearHistory (); //清除当前webview访问的历史记录，只会webview访问历史记录里的所有记录除了当前访问记录.
             clearFormData () //这个api仅仅清除自动完成填充的表单数据，并不会清除WebView存储到本地的数据。
     */



    /**
     WebView Cookies
     添加Cookies
         public void synCookies() {
             if (!CacheUtils.isLogin(this)) return;
             CookieSyncManager.createInstance(this);
             CookieManager cookieManager = CookieManager.getInstance();
             cookieManager.setAcceptCookie(true);
             cookieManager.removeSessionCookie();//移除
             String cookies = PreferenceHelper.readString(this, AppConfig.COOKIE_KEY, AppConfig.COOKIE_KEY);
             KJLoger.debug(cookies);
             cookieManager.setCookie(url, cookies);
             CookieSyncManager.getInstance().sync();
         }
     清除Cookies
        CookieManager.getInstance().removeSessionCookie();
     */



    /**
     WebView本地资源访问
         当我们在WebView中加载出从web服务器上拿取的内容时，是无法访问本地资源的，
         如assets目录下的图片资源，因为这样的行为属于跨域行为（Cross-Domain），
         而WebView是禁止的。解决这个问题的方案是把html内容先下载到本地，
         然后使用loadDataWithBaseURL加载html。这样就可以在html中使用 file:///android_asset/xxx.png
         的链接来引用包里面assets下的资源了。
        private void loadWithAccessLocal(final String htmlUrl) {
            new Thread(new Runnable() {
                public void run() {
                    try {
                        final String htmlStr = NetService.fetchHtml(htmlUrl);
                        if (htmlStr != null) {
                            TaskExecutor.runTaskOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    loadDataWithBaseURL(htmlUrl, htmlStr, "text/html", "UTF-8", "");
                                }
                            });
                            return;
                        }
                    } catch (Exception e) {
                        Log.e("Exception:" + e.getMessage());
                    }
                    TaskExecutor.runTaskOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            onPageLoadedError(-1, "fetch html failed");
                        }
                    });
                }
            }).start();
        }
     */


    /**
     如何避免WebView内存泄露？
         从网络上下载html的过程应放在工作线程中
         html下载成功后渲染出html的步骤应放在UI主线程，不然WebView会报错
         html下载失败则可以使用我们前面讲述的方法来显示自定义错误界面
     1 不在xml中定义 Webview ，而是在需要的时候在Activity中创建，并且Context使用 getApplicationgContext()
        new WebView(getApplicationContext());
     2 在 Activity 销毁（ WebView ）的时候，先让 WebView 加载null内容，然后移除 WebView，再销毁 WebView，最后置空。
         @Override
         protected void onDestroy() {
             if (mWebView != null) {
                 mWebView.loadDataWithBaseURL(null, "", "text/html", "utf-8", null);
                 mWebView.clearHistory();
                 ((ViewGroup) mWebView.getParent()).removeView(mWebView);
                 mWebView.destroy();
                 mWebView = null;
             }
             super.onDestroy();
         }
     */



    private class MyWebChromeClient extends WebChromeClient {
        @Override
        public void onProgressChanged(WebView view, int newProgress) {
            super.onProgressChanged(view, newProgress);
            //获得网页的加载进度，显示在右上角的TextView控件中
        }

        @Override
        public void onReceivedTitle(WebView view, String title) {
            super.onReceivedTitle(view, title);
            //获取Web页中的title用来设置自己界面中的title, 当加载出错的时候，
            //比如无网络，这时onReceiveTitle中获取的标题为”找不到该网页”,
        }

        @Override
        public void onReceivedIcon(WebView view, Bitmap icon) {
            super.onReceivedIcon(view, icon);
            //获取Web页中的icon

        }

        @Override
        public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {


            return super.onCreateWindow(view, isDialog, isUserGesture, resultMsg);
        }

        @Override
        public void onCloseWindow(WebView window) {
            super.onCloseWindow(window);
        }

        @Override
        public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
            //处理alert弹出框，html 弹框的一种方式

            return super.onJsAlert(view, url, message, result);
        }

        @Override
        public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {
            //处理confirm弹出框

            return super.onJsPrompt(view, url, message, defaultValue, result);
        }

        @Override
        public boolean onJsConfirm(WebView view, String url, String message, JsResult result) {
            //处理prompt弹出框

            return super.onJsConfirm(view, url, message, result);
        }
    }

    private class MyWebViewClient extends WebViewClient{

//        shouldOverrideUrlLoading()方法在API >= 24时被标记deprecated, 它的替代方法是
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
            //复写shouldOverrideUrlLoading()方法，使得打开网页时不调用系统浏览器， 而是在本WebView中显示
//            if (Uri.parse(url).getHost().equals("github.com/guoxiaoxing")) {
//                //如果是自己站点的链接, 则用本地WebView跳转
//                view.loadUrl(request.toString());
//                return false;
//            }
//            //如果不是自己的站点则launch别的Activity来处理
//            Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
//            mContext.startActivity(intent);

            //返回true: Android 系统会处理URL, 一般是唤起系统浏览器。
            //返回false: 当前 WebView 处理URL。  由于默认放回false,
            // 如果我们只想在WebView内处理链接跳转只需要设置mWebView.setWebViewClient(new WebViewClient())即可
            return true;
        }


        @Override
        public void onPageStarted(WebView view, String url, Bitmap favicon) {
            super.onPageStarted(view, url, favicon);
            //开始载入页面调用的，我们可以设定一个loading的页面，告诉用户程序在等待网络响应
        }

        @Override
        public void onPageFinished(WebView view, String url) {
            super.onPageFinished(view, url);
            //在页面加载结束时调用。我们可以关闭loading 条，切换程序动作
        }

        @Override
        public void onLoadResource(WebView view, String url) {
            super.onLoadResource(view, url);
            //在加载页面资源时会调用，每一个资源（比如图片）的加载都会调用一次。
        }

        @Override
        public void onPageCommitVisible(WebView view, String url) {
            super.onPageCommitVisible(view, url);
        }

        @Override
        public WebResourceResponse shouldInterceptRequest(WebView view, WebResourceRequest request) {
            return super.shouldInterceptRequest(view, request);
        }

        @Override
        public void onReceivedError(WebView view, WebResourceRequest request, WebResourceError error) {
            super.onReceivedError(view, request, error);
            //加载页面的服务器出现错误时（如404）调用。
        }

        @Override
        public void onReceivedHttpError(WebView view, WebResourceRequest request, WebResourceResponse errorResponse) {
            super.onReceivedHttpError(view, request, errorResponse);
        }

        @Override
        public void onFormResubmission(WebView view, Message dontResend, Message resend) {
            super.onFormResubmission(view, dontResend, resend);
        }

        @Override
        public void doUpdateVisitedHistory(WebView view, String url, boolean isReload) {
            super.doUpdateVisitedHistory(view, url, isReload);
        }

        @Override
        public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {
            super.onReceivedSslError(view, handler, error);
            //处理https请求   webView默认是不处理https请求的，页面显示空白
            //handler.proceed();    //表示等待证书响应
            // handler.cancel();      //表示挂起连接，为默认方式
            // handler.handleMessage(null);    //可做其他处理
        }

        @Override
        public void onReceivedClientCertRequest(WebView view, ClientCertRequest request) {
            super.onReceivedClientCertRequest(view, request);
        }

        @Override
        public void onReceivedHttpAuthRequest(WebView view, HttpAuthHandler handler, String host, String realm) {
            super.onReceivedHttpAuthRequest(view, handler, host, realm);
        }

        @Override
        public boolean shouldOverrideKeyEvent(WebView view, KeyEvent event) {
            return super.shouldOverrideKeyEvent(view, event);
        }

        @Override
        public void onUnhandledKeyEvent(WebView view, KeyEvent event) {
            super.onUnhandledKeyEvent(view, event);
        }

        @Override
        public void onScaleChanged(WebView view, float oldScale, float newScale) {
            super.onScaleChanged(view, oldScale, newScale);
        }

        @Override
        public void onReceivedLoginRequest(WebView view, String realm, String account, String args) {
            super.onReceivedLoginRequest(view, realm, account, args);
        }

        @Override
        public boolean onRenderProcessGone(WebView view, RenderProcessGoneDetail detail) {
            return super.onRenderProcessGone(view, detail);
        }
    }

