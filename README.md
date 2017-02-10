我们经常遇到过这样的需求
后台传了一段html过来,比如

	我是夏雨,这是<a href="http://blog.csdn.net/yulyu">我的博客</a>,<br/>更多android技术分享<a href="http://blog.csdn.net/yulyu">就在这里</a>
我们要显示这段文字,并且获取里面的a标签的点击事件,效果如下
![这里写图片描述](http://img.blog.csdn.net/20160818210436730)

其实android自带的textview就能够解析很多标签,只需要简单设置一下

        mTv.setText(Html.fromHtml(url));//解析html
        mTv.setMovementMethod(LinkMovementMethod.getInstance());//设置可点击
  ![夏雨](http://img.blog.csdn.net/20160818210536878)
 这样默认是用浏览器打开a标签里面的链接,但是如果要获取到a标签的点击事件和链接的话,得换一个方法
#####1 .在textview的布局文件里面加入这个属性

	android:autoLink="all"
#####2  .解析(复制下面两个方法)
	
	private void setLinkClickable(final SpannableStringBuilder clickableHtmlBuilder,
	        final URLSpan urlSpan) {
	    int start = clickableHtmlBuilder.getSpanStart(urlSpan);
	    int end = clickableHtmlBuilder.getSpanEnd(urlSpan);
	    int flags = clickableHtmlBuilder.getSpanFlags(urlSpan);
	        ClickableSpan clickableSpan = new ClickableSpan() {
	
	            public void onClick(View view) {
	                //Do something with URL here.
	                String url = urlSpan.getURL();
	                Toast.makeText(MainActivity.this,url,Toast.LENGTH_SHORT).show();
	                System.out.println(url);
	            }
	//            public void updateDrawState(TextPaint ds) {
	//                //设置颜色
	//                ds.setColor(Color.argb(255, 54, 92, 124));
	//                //设置是否要下划线
	//                ds.setUnderlineText(false);
	//            }
	
	        };
	    clickableHtmlBuilder.setSpan(clickableSpan, start, end, flags);
	}
	
	private CharSequence getClickableHtml(String html) {
	    Spanned spannedHtml = Html.fromHtml(html);
	    SpannableStringBuilder clickableHtmlBuilder = new SpannableStringBuilder(spannedHtml);
	    URLSpan[] urls = clickableHtmlBuilder.getSpans(0, spannedHtml.length(), URLSpan.class);
	    for (final URLSpan span : urls) {
	        setLinkClickable(clickableHtmlBuilder, span);
	    }
	    return clickableHtmlBuilder;
	}

#####3 .在代码中使用

	mTv.setText(getClickableHtml(url));
	mTv.setMovementMethod(LocalLinkMovementMethod.getInstance());
这样就能够有如下效果啦,很简单吧.
![夏雨](http://img.blog.csdn.net/20160818210436730)

今天就先这样吧,在后续的文章中我会再总结一下更复杂的标签解析,比如表情,a标签以及超链接同时存在的情况.

虽然这篇文章很简单,但也希望能够给你带来一点点收获 o(∩_∩)o 

