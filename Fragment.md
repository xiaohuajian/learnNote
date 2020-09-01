## 使用时注意点

### Android Fragment内onActivityResult方法不被调用的解决方法

1. Fragment 内代码 启动的时候

   ```
               Intent intent = new Intent(getActivity(), PubActivity.class);
               intent.putExtra("titleList", (Serializable) titleList);
               // 注意 这个 getActivity 要加上，否则会出错
               getActivity().startActivityForResult(intent, REQ_CODE);
   ```

   

2. Activity 内的onActivityResult 方法的重写：

   ```
   override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
           super.onActivityResult(requestCode, resultCode, data)
           LogUtil.debugLog("requestCode " , "acitivity" + requestCode.toString())
           //这里要调用对应的fragment 进行 onActivityResult 方法进行调用
           fromPhotoFragment?.onActivityResult(requestCode, resultCode, data)
   ```

3. fragment 就直接重写并接收相应的onActivityResult 结果。



