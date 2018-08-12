# ruby-opencv

An OpenCV wrapper for Ruby.

* Web site: <https://github.com/ruby-opencv/ruby-opencv>
* Ruby 2.x and OpenCV 3.4.1 are supported.

## Requirement

* OpenCV <http://opencv.org/>
  * [Download](http://sourceforge.net/projects/opencvlibrary/)
  * [Install guide](http://docs.opencv.org/doc/tutorials/introduction/table_of_content_introduction/table_of_content_introduction.html#table-of-content-introduction)

## Install
### Linux/Mac
1. Install [OpenCV](http://sourceforge.net/projects/opencvlibrary/files/opencv-unix/)
2. Install ruby-opencv

```
$ gem install ruby-opencv -- --with-opencv-dir=/path/to/opencvdir
```

Note: **/path/to/opencvdir** is the directory where you installed OpenCV.


### Windows
You can use pre-build binary for Windows (mswin32).

1. Install [OpenCV](http://sourceforge.net/projects/opencvlibrary/files/opencv-win/)
2. Set path to OpenCV libraries. When you installed OpenCV to **C:\opencv**, add **C:\opencv\build\x86\vc10\bin** to the systems path.
3. Install ruby-opencv

```
$ gem install ruby-opencv
```

## Sample code
### Load and Display an Image

A sample to load and display an image. An equivalent code of [this tutorial](http://docs.opencv.org/doc/tutorials/introduction/display_image/display_image.html#display-image).

```ruby
require 'opencv'

if ARGV.size == 0
  puts "Usage: ruby #{__FILE__} ImageToLoadAndDisplay"
  exit
end

image = nil
begin
  image = Cv::imread(ARGV[0], Cv::CV_LOAD_IMAGE_COLOR) # Read the file.
rescue
  puts 'Could not open or find the image.'
  exit
end

window = Cv::Window.new('Display window') # Create a window for display.
window.show(image) # Show our image inside it.
Cv::wait_key # Wait for a keystroke in the window.
```

### Face Detection

A sample to detect faces from an image.

```ruby
require 'opencv'

if ARGV.length < 1
  puts "Usage: ruby #{__FILE__} image"
  exit
end

classifier = Cv::CascadeClassifier.new('examples/haarcascade_frontalface_alt.xml')
image = Cv::imread(ARGV[0], -1)

color = Cv::Scalar.new(0, 255, 255)
classifier.detect_multi_scale(image).each do |r|
  pt1 = Cv::Point.new(r.x, r.y)
  pt2 = Cv::Point.new(r.x + r.width, r.y + r.height)
  image.rectangle!(pt1, pt2, color, thickness: 3, line_type: Cv::CV_AA)
end

window = Cv::Window.new('Face detection')
window.show(image)
Cv::wait_key
```

### Image Classification

A samples to classify objects in an image.

```ruby
require 'opencv'

classes = []
File.open("./examples/synset_words.txt", "r") do |f|
  f.each_line { |line|
    _, value = line.strip.split(" ", 2)
    classes << value.split(",", 2).first
  }
  f.close
end

net = Cv::Dnn.read_net_from_caffe("./examples/bvlc_googlenet.prototxt", "./examples/bvlc_googlenet.caffemodel")
net.set_input(Cv::Dnn.blob_from_image(Cv.imread("./examples/images/stuff.jpg", Cv::IMREAD_UNCHANGED), size: Cv::Size.new(224, 224), mean: Cv::Scalar.new(104, 117, 123)))
predictions = net.forward

for i in 0..(predictions.cols - 1)
  confidence = predictions.at(0, i)[0]
  puts "#{classes[i]} #{confidence}" if confidence > 0.1
end
```

For more samples, see examples/*.rb

## LICENSE:

The MIT Liscense

see LICENSE.txt
