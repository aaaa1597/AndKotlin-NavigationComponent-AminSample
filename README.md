# AndKotlin-NavigationComponent-AminSample
前回作った [AndKotlin-NavigationComponentSample](https://github.com/aaaa1597/AndKotlin-NavigationComponentSample.git) に画面遷移アニメーションを追加した版  
参考:[https://at-sushi.work/blog/21/](https://at-sushi.work/blog/21/) あざっす。

## Navigation Component
今の今まで Navigation Component を触らずにやってきたけど、いい加減士っとく必要があるかと思って調べてみた。  
早い話、ActivityとかFragmentとかの画面遷移をGUIで作れるようにしたやつ。実体はxmlファイルだけど、それをAndroid Studioで開くと図として表示してくれる。 状態遷移図が必要になりそうな時に Navigation Component で作っとくときっと幸せになれる。 必要になってからだもう遅い(いろいろ作り直しになると思われ)。

## ざっくり手順
1. Fragmentを準備
2. Navigationリソース(Resource Type = Navigation)を生成
3. Destination：デスティネーション(遷移先)を追加
4. ActivityにNavigation Componentをくっつける。
5. 他のFragmentもデスティネーション(遷移先)に設定する。
6. 画面遷移処理を実装する

## 1. Fragmentを準備
まぁ適当に。

## 2. Navigationリソース(Resource Type = Navigation)を生成
<img src="image.png" width=500><br/>

  File name: aaanavi  
  Resource type: Navigation  
  後はデフォルトで

<img src="image-1.png" width=500>

OK押下  
↓  
res\navigation配下にaaanaviが出来る。  
ダブルクリックで開いとく  
<img src="image-3.png" width=500>

以下この操作での変更点  
1. res/navigation/aaanavi.xmlファイルを新規作成
2. build.gradle.ktsのdependenciesにimplを追加
3. libs.versions.tomlに定義を追加

- res/navigation/aaanavi.xmlファイルを新規作成
```diff xml:aaanavi.xml
+ <?xml version="1.0" encoding="utf-8"?>
+ <navigation xmlns:android="http://schemas.android.com/apk/res/android"
+     xmlns:app="http://schemas.android.com/apk/res-auto"
+     android:id="@+id/aaanavi">
+ 
+ </navigation>
```

- build.gradle.kts(45-46)のdependenciesにimplを追加
```diff kot:build.gradle.kts
+   implementation(libs.androidx.navigation.fragment.ktx)
+   implementation(libs.androidx.navigation.ui.ktx)
```

- libs.versions.toml(12-13,24-25)に定義を追加
```diff toml:libs.versions.toml
+ navigationFragmentKtx = "2.9.0"
+ navigationUiKtx = "2.9.0"

+ androidx-navigation-fragment-ktx = { group = "androidx.navigation", name = "navigation-fragment-ktx", version.ref = "navigationFragmentKtx" }
+ androidx-navigation-ui-ktx = { group = "androidx.navigation", name = "navigation-ui-ktx", version.ref = "navigationUiKtx" }
```

## 3. Destination：デスティネーション(遷移先)を追加
ActivityやらFragmentやらを遷移先に指定できる。  
※ちなみにこの操作して、build→実行してもまだ何も表示されない  

下図の赤丸アイコンを押下 → 遷移先を追加

<img src="image-2.png" width=500>

<br/>↓ こうなった<br/>

<img src="image-4.png" width=500>

この操作での変更点  
1. res/navigation/aaanavi.xmlファイルにfragmentの定義を追加

- res/navigation/aaanavi.xml(4,6,8-12)
```diff xml:aaanavi.xml
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
+   xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/aaanavi"
+   app:startDestination="@id/mainFragment">

+   <fragment
+       android:id="@+id/mainFragment"
+       android:name="com.aaa.navigationcomponent.MainFragment"
+       android:label="fragment_main"
+       tools:layout="@layout/fragment_main" />
```

## 4. ActivityにNavigation Componentをくっつける。

1. Activityに設定しているlayautファイル(activity_main.xml)を開く
2. Design画面を開いとく
3. frag と入力。 NavHostFragmentを検索
4. NavHostFragment を activity_main.xmlにDrag & Drop

<img src="image-6.png" width=500>

<br/>↓<br/>
5. 出てきたpopupで手順3で設定したFragment(or Acivity)を選択 → OK

<img src="image-8.png" width=500>

この操作での変更点  
1. activity_main.xmlファイルに FragmentContainerView を追加

- activity_main.xml(16-25)
```diff xml:activity_main.xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fcv_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

+   <androidx.fragment.app.FragmentContainerView
+       android:id="@+id/fragmentContainerView"
+       android:name="androidx.navigation.fragment.NavHostFragment"
+       android:layout_width="170dp"
+       android:layout_height="8dp"
+       app:defaultNavHost="true"
+       app:navGraph="@navigation/aaanavi"
+       tools:layout_editor_absoluteX="1dp"
+       tools:layout_editor_absoluteY="182dp" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

うーん、fragmentのコンテナとしてfcv_containerを準備してたのに、勝手に別のFragmentContainerViewを追加されてしまった。準備してたfcv_containerへの追加の方法はわかんない。

手で修正する。

- activity_main.xml(13,16-17,19-25)
```diff xml:activity_main.xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fcv_container"
+       android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
+       app:defaultNavHost="true"
+       app:navGraph="@navigation/aaanavi" />

-   <androidx.fragment.app.FragmentContainerView
-       android:id="@+id/fragmentContainerView"
-       android:name="androidx.navigation.fragment.NavHostFragment"
-       android:layout_width="170dp"
-       android:layout_height="8dp"
-       app:defaultNavHost="true"
-       app:navGraph="@navigation/aaanavi"
-       tools:layout_editor_absoluteX="1dp"
-       tools:layout_editor_absoluteY="182dp" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

※ここまでやったら、ビルド→実行で画面が表示される様になった。  
<img src="Screenshot-1.png" width=150>


## 5. 他のFragmentもデスティネーション(遷移先)に設定する。

手順3(デスティネーション(遷移先)を追加)の要領で、デスティネーションを追加
<img src="image-5.png" width=500>

↓  
マウス操作で、丸のアイコンのやつをつかんで、  
<img src="image-7.png" width=300>  

↓  
遷移先の画面にDropする。  
<img src="image-9.png" width=300>  

↓  
繋がる。  
<img src="image-10.png" width=300> 

↓  
同じ操作を下の SubFragment2 にも実行する。  
<img src="image-11.png" width=300> 

この操作での変更点  
1. aaanavi.xmlファイルに actionの定義とFrgmentの定義を追加

- res/navigation/aaanavi.xml(12-29)
```diff xml:aaanavi.xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/aaanavi"
    app:startDestination="@id/mainFragment">

    <fragment
        android:id="@+id/mainFragment"
        android:name="com.aaa.navigationcomponent.MainFragment"
        android:label="fragment_main"
        tools:layout="@layout/fragment_main" >
+       <action
+           android:id="@+id/action_mainFragment_to_subFragment12"
+           app:destination="@id/subFragment1" />
+       <action
+           android:id="@+id/action_mainFragment_to_subFragment2"
+           app:destination="@id/subFragment2" />
    </fragment>
+   <fragment
+       android:id="@+id/subFragment1"
+       android:name="com.aaa.navigationcomponent.SubFragment1"
+       android:label="fragment_sub1"
+       tools:layout="@layout/fragment_sub1" />
+   <fragment
+       android:id="@+id/subFragment2"
+       android:name="com.aaa.navigationcomponent.SubFragment2"
+       android:label="fragment_sub2"
+       tools:layout="@layout/fragment_sub2" />
</navigation>
```

## 6. 画面遷移処理を実装する

1. Fragment::findNavController()で、NavControllerを取得
2. NavController.navigateを呼ぶ

<br/>
NavControllerの取得方法は他にもあって以下の通り。

   1. Fragment.findNavController()
   2. View.findNavController()
   3. Activity.findNavController(viewId: Int)

実装

- fragment_main.xml(31-49)  ← 画面遷移用のボタンを追加してなかった。。。

```diff xml:fragment_main.xml
  ～略～
+   <Button
+       android:id="@+id/btn_callsub1"
+       android:layout_width="wrap_content"
+       android:layout_height="wrap_content"
+       android:text="@string/callSubfragment1"
+       app:layout_constraintTop_toBottomOf="@id/imv_nyandar001"
+       app:layout_constraintLeft_toLeftOf="parent"
+       app:layout_constraintRight_toLeftOf="@id/btn_callsub2"
+       app:layout_constraintHorizontal_chainStyle="packed"/>

+   <Button
+       android:id="@+id/btn_callsub2"
+       android:layout_width="wrap_content"
+       android:layout_height="wrap_content"
+       android:text="@string/callSubfragment2"
+       app:layout_constraintTop_toBottomOf="@id/imv_nyandar001"
+       app:layout_constraintLeft_toRightOf="@id/btn_callsub1"
+       app:layout_constraintRight_toRightOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

- MainFragment.kt(8-11, 42-55)

```diff kotlin:MainFragment.kt
  ～略～

+ import android.view.ViewGroup
+ import android.widget.Button
+ import androidx.core.os.bundleOf
+ import androidx.navigation.fragment.NavHostFragment
+ import androidx.navigation.fragment.findNavController

  ～略～

+   override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
+       super.onViewCreated(view, savedInstanceState)
+
+       view.findViewById<Button>(R.id.btn_callsub1).setOnClickListener {
+           val params = bundleOf("aaa" to "aaa", "bbb" to "bbb")
+           findNavController().navigate(R.id.action_mainFragment_to_subFragment12, params)
+       }
+
+       view.findViewById<Button>(R.id.btn_callsub2).setOnClickListener {
+           val params = bundleOf("aaa" to "aaa", "bbb" to "bbb")
+           findNavController().navigate(R.id.action_mainFragment_to_subFragment2, params)
+       }
+   }
+
  ～略～
```

実質の変更部分は、findNavController().navigate()の呼出だけ。  
これで、画面遷移の実装ができた。  
引数も期待通り受け渡しできてる。

