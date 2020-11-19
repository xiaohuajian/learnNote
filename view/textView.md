## 介绍
本篇章主要介绍textView 一些在开发中经常用到的点，方便自己日后查阅。

### 文字添加划线

```java
中划线：
textview.getPaint().setFlags(Paint.STRIKE_THRU_TEXT_FLAG);

下划线：
textview.getPaint().setFlags(Paint.UNDERLINE_TEXT_FLAG);

取消
textview.getPaint().setFlags（0）
```


### 动态设置drawableLeft、right
法一：这个更加常用和方便

```java
    Drawable rightDrawable = mContext.getResources().getDrawable(R.drawable.arrow_right);
    saleCardViewHolder.couponDetailTv.setCompoundDrawablesWithIntrinsicBounds(null, null, rightDrawable, null);
saleCardViewHolder.couponDetailTv.setCompoundDrawablePadding(Dp2PxUtil.dip2px(mContext, 5F));
```

法二：设置drawable 之前，drawable要设置bounds，否则会出错
```java
Drawable rightDrawable = mContext.getResources().getDrawable(R.drawable.arrow_right);
rightDrawable.setBounds(0, 0, rightDrawable.getMinimumWidth(), rightDrawable.getMinimumHeight());
saleCardViewHolder.couponDetailTv.setCompoundDrawables(null, null, rightDrawable, null);
saleCardViewHolder.couponDetailTv.setCompoundDrawablePadding(Dp2PxUtil.dip2px(mContext, 5F));
```

注意：如果一个TextView 宽度为整个屏幕，这时候如果需要drawableLef 和文字居中显示，用gravity = center， 发现只有文字居中，drawable 没有居中，这时候可以怎么办呢？

其实可以想一想 textView 怎么画drawable ，弄清楚这个地方，可以自定义重写这一块。（参考 [链接](https://www.cnblogs.com/annieBaby/p/6635472.html)， 但这个效果并不理想，就是drawable 和文字之间的距离无法控制，可以在深究一下 如何实现更好的效果）

### 一段文字显示不同颜色 
这也是一个常见的需求，当我们不想用多个控件实现时，整体来说有这样的几种方式：

```java
// 这种方式比较灵活，可以针对不同国家的语言做出不同的响应 ；另外可以同时作用大小和颜色、斜体、加粗等html 样式
   <string name="renewal_loan_text_tip"> <![CDATA[
    <b style="color: #31445D">Plg Yth, mohon perhatikan bahwa Anda hanya <i><font color=#FFCD3C>akan mendapatkan 1 pinjaman</font></i> dari yang Anda pilih, berdasarkan skor kredit Anda.</b>
]]></string>

 textViewContent.setText(Html.fromHtml(mContext.getResources().getString(R.string.renewal_loan_text_tip)));
```
还有一种就是通过span 来实现，有颜色、字体大小、下划线、删除线、点击事件各种sapn， 然后根据需求来去使用；也可以把这些span 组合起来，效果就比较强大了。举两个例子：

```java
SpannableString styledText = new SpannableString("Android，你好");
styledText.setSpan(new TextAppearanceSpan(this, R.style.style0), 0, 3, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
styledText.setSpan(new TextAppearanceSpan(this, R.style.style1), 3, 5, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
         
mTextView.setText(styledText, TextView.BufferType.SPANNABLE);
```


```
// 回复内容
String originPrex = mContext.getString(R.string.student_text_feed_up_colon);
String originContent = item.getContent();
String originPic = "";
if (item.pic.size() > 0){
    originPic = " [图片]";
}
String origin =originPrex +  originContent + originPic;
SpannableStringBuilder ssbOrigin = new SpannableStringBuilder(origin);
ForegroundColorSpan foregroundColorSpanPre =new ForegroundColorSpan(mContext.getResources().getColor(R.color.text_262626));
ForegroundColorSpan foregroundColorSpanOriginContent =new ForegroundColorSpan(mContext.getResources().getColor(R.color.text_BFBFBF));
ClickableSpan clickableSpanOrigin =new ClickableSpan() {
    @Override
    public void onClick(View widget) {
        ImagePreviewActivity.actionStart(mContext, item.pic);
    }

    @Override
    public void updateDrawState(TextPaint ds) {
        //去掉可点击文字的下划线
        ds.setUnderlineText(false);
        //设置点击clickSpan 文字颜色
        ds.setColor(mContext.getResources().getColor(R.color.text_00BAFF));
    }
};
ssbOrigin.setSpan(foregroundColorSpanPre, 0, originPrex.length(), Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
ssbOrigin.setSpan(foregroundColorSpanOriginContent, originPrex.length(), (originPrex + originContent).length() , Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
if (item.pic.size() > 0){
    ssbOrigin.setSpan(clickableSpanOrigin, (originPrex + originContent).length(), origin.length() , Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
    // 要加上这句话 否则点击span 不生效
    holder.tvContent.setMovementMethod(LinkMovementMethod.getInstance());
}
holder.tvContent.setText(ssbOrigin);
```

关于span 可以参考：[span](https://blog.csdn.net/zrbcsdn/article/details/53186948)。