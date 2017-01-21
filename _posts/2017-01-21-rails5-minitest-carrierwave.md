---
layout: post
title: 使用Rails5的Minitest测试CarrierWave上传文件
---

使用Minitest测试CarrierWave上传图片功能，需要设置测试环境中CarrierWave的根目录。

``` ruby
CarrierWave.root = root_directory
```

首先，创建根目录 `test/fixtures/files/uploads`，把待上传的文件放到 `test/fixtures/files` 中。

其次，运行测试打印 `model.file_field.file.path` 的结果，会得到类似下面的结果。

`/Users/mike/proj/public/uploads/user/picture/762146111/mike.jpg`

准备测试数据：

``` ruby
mike:
  name: Mike
  picture: mike.jpg
```

手动创建 `test/fixtures/files/uploads/user/picture/762146111` 目录，把待上传的文件复制到该目录中。

最后，运行测试就可以取到测试数据中的图片信息。

`test/test_helper.rb` 文件中的配置如下：

``` ruby
require 'fileutils'

# create root dir
FileUtils.mkdir_p carrierwave_root

# Carrierwave setup and teardown
carrierwave_template = Rails.root.join('test','fixtures','files')
carrierwave_root = Rails.root.join('test','support','carrierwave')

# Carrierwave configuration is set here instead of in initializer
CarrierWave.configure do |config|
  config.root = carrierwave_root
  config.enable_processing = false
  config.storage = :file
  config.cache_dir = Rails.root.join('test','support','carrierwave',
    'carrierwave_cache')
end

# And copy carrierwave template in
FileUtils.cp_r carrierwave_template.join('uploads'), carrierwave_root

at_exit do
  # remove root dir
  FileUtils.remove_dir carrierwave_root

  #puts "Removing carrierwave test directories:"
  Dir.glob(carrierwave_root.join('*')).each do |dir|
    #puts "   #{dir}"
    FileUtils.remove_entry(dir)
  end
end
```
