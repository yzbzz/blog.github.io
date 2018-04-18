---
title: android-view-textview
date: 2018-04-10 16:33:20
tags:
---

```java
public class PartiallyClickableActivity extends AppCompatActivity {

    private TextView tv;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.partially_clickable_activity);

        tv = (TextView) findViewById(R.id.tv);

        //step1 设置点击背景色为透明
        tv.setHighlightColor(getResources().getColor(android.R.color.transparent));
        String st1 = "产品成立、计息并进封入闭期。购买成功后1-2个交易日可通过";
        String st2 = "【我的理财】";


        //step3 设置SpannableString
        SpannableString info = new SpannableString(st1 + st2 );
        info.setSpan(new ForegroundColorSpan(Color.BLACK), 0, st1.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        info.setSpan(new ForegroundColorSpan(Color.RED), st1.length(), (st1 + st2).length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        info.setSpan(new Clickable(clickListener), st1.length(), (st1 + st2).length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE | ~Spanned.SPAN_MARK_MARK);

        //step4 TextView设置SpannableString
        tv.setText(info);
        tv.setMovementMethod(LinkMovementMethod.getInstance());
    }

    private View.OnClickListener clickListener = new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            Toast.makeText(PartiallyClickableActivity.this, "点击成功....【我的理财】", Toast.LENGTH_SHORT).show();
        }
    };

    //step2 定义ClickableSpan
    class Clickable extends ClickableSpan {
        private final View.OnClickListener mListener;

        public Clickable(View.OnClickListener l) {
            mListener = l;
        }


        @Override
        public void onClick(View v) {
            mListener.onClick(v);
        }

        @Override
        public void updateDrawState(TextPaint ds) {
            //取消下滑线
            ds.setUnderlineText(false);
//          super.updateDrawState(ds);
        }
    }
}
```

