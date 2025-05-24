# AndKotlin-NavigationComponent-AminSample
前回作った [AndKotlin-NavigationComponentSample](https://github.com/aaaa1597/AndKotlin-NavigationComponentSample.git) に画面遷移アニメーションを追加した版  
参考:[https://at-sushi.work/blog/21/](https://at-sushi.work/blog/21/) あざっす。

## 概要
前回のプロジェクトに画面遷移アニメーションを追加する。  
Navigation Component のおかげかそんなに変更しなくてもイケた。

## ざっくり手順
1. アニメーションxmlを準備
2. actionを追加
3. 画面遷移を実装する

## 1. アニメーションxmlを準備
今回は[https://at-sushi.work/blog/21/](https://at-sushi.work/blog/21/)さんのを使わせてもらった。  
基本的なアニメーションは網羅してくれてるのでほとんどのプロジェクトで流用できる気がする。

res\animにxmlファイルを準備する。4ファイルで1セット。

- enterAnim ... 開始のアニメーション

- exitAnim ... 終了のアニメーション

- popEnterAnim ... Backボタン押下のときの開始アニメーション

- popExitAnim ... Backボタン押下のときの終了アニメーション

## 2. actionを追加

res\navigation配下のナビゲーション.xml(aaanavi.xml)にactionを追加していく。  
UIを操作するより、xmlコードを直接いじった方が早い。  

- aaanavi.xml(19-60)
```diff xml:aaanavi.xml
  ～略～
        <action
            android:id="@+id/action_mainFragment_to_subFragment2"
            app:destination="@id/subFragment2" />
+       <action
+           android:id="@+id/action_to_fade1"
+           app:destination="@id/subFragment1"
+           app:enterAnim="@anim/nav_default_enter_anim"
+           app:exitAnim="@anim/nav_default_exit_anim"
+           app:popEnterAnim="@anim/nav_default_pop_enter_anim"
+           app:popExitAnim="@anim/nav_default_pop_exit_anim" />
+       <action
+           android:id="@+id/action_to_slide2"
+           app:destination="@id/subFragment2"
+           app:enterAnim="@anim/nav_slide_enter_anim"
+           app:exitAnim="@anim/nav_slide_exit_anim"
+           app:popEnterAnim="@anim/nav_slide_pop_enter_anim"
+           app:popExitAnim="@anim/nav_slide_pop_exit_anim" />
+       <action
+           android:id="@+id/action_to_zoom1"
+           app:destination="@id/subFragment1"
+           app:enterAnim="@anim/nav_zoom_enter_anim"
+           app:exitAnim="@anim/nav_zoom_exit_anim"
+           app:popEnterAnim="@anim/nav_zoom_pop_enter_anim"
+           app:popExitAnim="@anim/nav_zoom_pop_exit_anim" />
+       <action
+           android:id="@+id/action_to_up2"
+           app:destination="@id/subFragment2"
+           app:enterAnim="@anim/nav_up_enter_anim"
+           app:exitAnim="@anim/nav_up_exit_anim"
+           app:popEnterAnim="@anim/nav_up_pop_enter_anim"
+           app:popExitAnim="@anim/nav_up_pop_exit_anim" />
+       <action
+           android:id="@+id/action_to_dynamic1"
+           app:destination="@id/subFragment1"
+           app:enterAnim="@anim/nav_dynamic_enter_anim"
+           app:exitAnim="@anim/nav_dynamic_exit_anim"
+           app:popEnterAnim="@anim/nav_dynamic_pop_enter_anim"
+           app:popExitAnim="@anim/nav_dynamic_pop_exit_anim" />
+       <action
+           android:id="@+id/action_to_dynamic2"
+           app:destination="@id/subFragment2"
+           app:enterAnim="@anim/nav_dynamic_enter_anim"
+           app:exitAnim="@anim/nav_dynamic_exit_anim"
+           app:popEnterAnim="@anim/nav_dynamic_pop_enter_anim"
+           app:popExitAnim="@anim/nav_dynamic_pop_exit_anim" />
    </fragment>
    <fragment
        android:id="@+id/subFragment1"
        android:name="com.aaa.navigationcomponent.SubFragment1"
        android:label="fragment_sub1"
        tools:layout="@layout/fragment_sub1" />
  ～略～
```

## 3. 画面遷移を実装する

画面遷移処理の呼出部分はコーディングする。

- MainFragment.kt(50-69)
```diff kotlin:MainFragment.kt
  ～略～
+       view.findViewById<Button>(R.id.btn_callsub1fade).setOnClickListener {
+           val params = bundleOf("aaa" to "aaa222", "bbb" to "bbb222")
+           findNavController().navigate(R.id.action_to_fade1, params)
+       }
+       view.findViewById<Button>(R.id.btn_callsub1zoom).setOnClickListener {
+           val params = bundleOf("aaa" to "aaa333", "bbb" to "bbb333")
+           findNavController().navigate(R.id.action_to_zoom1, params)
+       }
  ～略～
+        view.findViewById<Button>(R.id.btn_callsub2slide).setOnClickListener {
+           val params = bundleOf("aaa" to "aaa", "bbb555" to "bbb55")
+           findNavController().navigate(R.id.action_to_slide2, params)
+       }
+       view.findViewById<Button>(R.id.btn_callsub2up).setOnClickListener {
+           val params = bundleOf("aaa" to "aaa", "bbb555" to "bbb55")
+           findNavController().navigate(R.id.action_to_up2, params)
+       }
  ～略～
```

これで、画面遷移のアニメーション実装ができた。  
引数も期待通り受け渡しできてる。  
アニメーションだけを拡張するだけなら結構分かりやすかった。


# 番外編 画面遷移時に、遷移先Fragmentに引数を渡す。
1. 送信側で: findNavController().navigate()を呼ぶときにBundle型の引数渡す
  1-1. Bundle型の引数を生成
  1-2. navigate()の第2引数にセット
2. 受信側で: onCreate()の中で、argumentsメンバから取り出す。

### 1. 送信側で: findNavController().navigate()を呼ぶときにBundle型の引数渡す
- MainFragment.kt(47-48)
```diff kotlin:MainFragment.kt
  ～略～
        view.findViewById<Button>(R.id.btn_callsub1).setOnClickListener {
-           findNavController().navigate(R.id.action_mainFragment_to_subFragment1)
+           val params = bundleOf(ARG_PARAM1 to "aaa111", ARG_PARAM2 to "bbb111")
+           findNavController().navigate(R.id.action_mainFragment_to_subFragment1, params)
        }
  ～略～
```

### 2. 送信側で: findNavController().navigate()を呼ぶときにBundle型の引数渡す
- SubFragment1.kt(25-26,30-34)
```diff kotlin:SubFragment1.kt
  ～略～
+  private var param1: String? = null
+  private var param2: String? = null

   override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
+       arguments?.let {
+           param1 = it.getString(ARG_PARAM1)
+           param2 = it.getString(ARG_PARAM2)
+           Log.d("aaaaa", "aaaaa aaa=${param1} bbb=${param2}")
+       }
    }
  ～略～
```
