Whenever you're sure, that you will have more than 1 type of attachments, create a seperate Asset model, instead of storing them in several models as text fields.

The schema for the database table should be as following:


```ruby
create_table :assets, :force => true do |t|
  t.string   :type
  t.text     :file
  t.integer  :assetable_id
  t.string   :assetable_type
  t.timestamps
end

add_index :assets, [:assetable_id, :assetable_type]
add_index :assets, :type
```

The model utilizes **polymorphic** relations, as well as **STI** (Single Table Inheritance) in order to achieve flexibility in uploader selection. 

```ruby
class Asset extends ActiveRecord::Base
  attr_accessible :file
  belongs_to :assetable, polymorphic: true
  
  # simplify access to file's url and path
  delegate :url, :current_path, to: :file
end
```

It is recommended to treat this class as an abstract class, from which to inherit.

Now you can create z subclass and attach any uploader you want.

```ruby
class Assets::Image < Asset
  # You can process the uploaded images and create multiple versions
  mount_uploader :file, ImageUploader
end
```

```ruby
class Assets::Audio < Asset
  # You can process the uploaded audio files and convert them to other formats using ffmpeg
  mount_uploader :file, AudioUploader
end
```

With this setup, you get full isolation in your uploaders and can create seperate uploaders, for seperate file types. Also you don't have to write code like this:

```ruby
class UserAssetUploader < CarrierWave::Uploader::Base
  include CarrierWave::MiniMagick
  include CarrierWave::MimeTypes
  storage :file

  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  process :resize_to_fill => [854, 480], if: :foo?
  process :resize_to_fill => [960, 640], if: :bar?
  process :resize_to_fill => [1280, 720], if: :baz?
  process :resize_to_fill => [1152, 768], if: :biz?
  version :thumb, if: :image? do
    process convert: :png
    process :resize_and_pad => [278, 155]
  end
  
  def filename
    if foo?
      'foo'
    elsif bar?
      'bar'
    elsif baz?
      'baz'
    else
      'biz'
    end
  end

  protected
  def image?(new_file)
    new_file.content_type.include? 'image'
  end

  def foo(file = nil)
    self.model.is_foo?
  end

  def bar(file = nil)
    self.model.is_bar?
  end

  def baz(file = nil)
    self.model.is_baz?
  end

  def biz(file = nil)
    self.model.is_biz?
  end
end
```

Finally to use all of this in a model:
```ruby
class Document < ActiveRecord::Base
  has_one :audio_asset, as: :assetable, class_name: 'Attachments::Audio'
  has_many :image_assets, as: :assetable, class_name: 'Attachments::Image'
end
```
