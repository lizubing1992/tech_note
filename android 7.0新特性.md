###1.更强的权限控制，从Android 6.0的动态权限控制(Runtime Permissions)到Android7.0的"私有目录被限制访问"，"StritMode API政策"

###2.目录被限制访问 
####  2.1 Android7.0提高了私有文件的安全性，N以上更高的版本的私有目录将被限制访问
 私有文件的文件访问权限不在放权给所有的应用，使用MODE_WORLD_READABLE或是  MODE_WORLD_WRITEABLE进行操作讲触发SecurityException

 应对策略：无法通过FileAPI访问手机存储数据，一些基于FileAPI的文件浏览器也将受到影响，但是这些限制尚不能完全执行,这个是一个趋势

#### 2.2 给其他应用传递file://URI类型的Uri,可能会导致接受者无法访问路径，会导致FileUriExposedException 

 应对策略：使用FileProvider解决这个问题

#### 2.3 DownloadManager 不在按文件名分享私人的存储文件，COLUMN_LOCAL_FILENAME在Android7.0被标记为  ，旧版应用访问COLUMN_LOCAL_FILENAME可能路径无法访问，会触发SecurityException

 应对策略：通过ContentResolver.openFileDescriptor来访问DownloadManagerde的公开文件

###3.应用间共享文件

####  3.1强制使用StritMode API政策禁止使用应用外的File文件路劲，调用系统拍照，或者是裁剪照片

  应对策略：通过content://URI类型的Uri并且授予URI临时访问权限，授权的最简单方式是使用FileProvider类

####  3.2调用系统拍照，裁剪照片
  同样使用使用FileProvider类能解决问题

