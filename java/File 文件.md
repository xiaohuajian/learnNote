## 易错点

### mkdir 和mkdirs 创建文件夹

mkdir 只能创建一级文件，mkdirs 可以创建多级文件夹；

### 创建文件

1. 通过File.createTempFile 来创建 ==（推荐此创建方法）==

   ```
    fileDirectory = new File(Directory.AVATAR_TMPFILE_HEAD_PHOTO);
    fileDirectory.mkdirs();
    try {
    // 这时这个Directory 一定要存在，否则创建不成功
        mFile = File.createTempFile(System.currentTimeMillis() + "_file", ".jpg", fileDirectory);
     } catch (IOException e) {
         e.printStackTrace();
     }
   ```

   

2. file.creatNewFile  

   先把file 通过new File（） 的方法创建一下，在调用 file.creatNewFile  

3. 

### 在android中，一般的 用网络加载框架会有根据路径缓存

当我们使用imageLoader或者glide 加载一个本地图片时，比如头像，我们先通过相机拍一个图片并把这个图片存到本地，然后把路径传过去，这时候用框架显示出来；后面 再去拍照显示图片，发现显示的是之前拍的图片，这就是因为框架会根据路径进行缓存图片，造成这个原因的是我们两次传给框架的图片路径都是一样的，使得框架使用了缓存，所以这种情况，我们一般对图片命名加上一个时间戳来解决这个情况，并且每次创建图片的时把之前的图片文件夹都删除掉，避免造成过多的不必要的图片。