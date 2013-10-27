# HAARPHP 

## Further development on this project has stopped!!


__Feature Detection Library for PHP__

Based on [Viola-Jones Feature Detection Algorithm using Haar Cascades](http://www.cs.cmu.edu/~efros/courses/LBMV07/Papers/viola-cvpr-01.pdf)

This is a port of [OpenCV C++ Haar Detection](http://opencv.org/) (actually a port of [JViolaJones](http://code.google.com/p/jviolajones/) which is a port of OpenCV for Java) to PHP


![screenshot](/example-screenshot.png)


###Contents

* [How to Use](#how-to-use)
* [Haar Cascades](#where-to-find-haar-cascades-xml-files-to-use-for-feature-detection)
* [Todo](#todo)
* [Changelog](#changelog)


###How to Use
You can use the __existing openCV cascades__  to build your detectors.

To do this just transform the __opencv xml file__ to *PHP* format
using the __haartophp__ (php) tool (in cascades folder)

__examples:__

to use opencv's *haarcascades_frontalface_alt.xml*  in *php* do:

```bash
haartophp haarcascades_frontalface_alt.xml > haarcascades_frontalface_alt.php
```

this creates a php file:   *haarcascades_frontalface_alt.php*
which you can include in your php application (see examples)

the variable to use in php is similarly  
*$haarcascades_frontalface_alt*


__IMPORTANT__ : The conversion process has changed from previous versions (both in the CLI script and in HAARPHP)

The changes are :

* the feature rectangle coordinates are stored in an array instead of an object hash
* the tilted flag/attribute for rectangles (Rainer Lienhart et al.) is incorporated in the conversion

so __make sure to re-convert your .php xml cascades__ for this version of HAARPHP

The tilted attribute for rectangles (Rainer Lienhart et al. extension of the algorithm) describes tilted (rotated 45 deg) features (rectangles)
in order to detect more features at rotated positions

The tilted flag is added in the *.php* file (as *tilt* ), 
(see also [HAAR.js](https://github.com/foo123/HAAR.js))

This is now incorporated into HAARPHP and this increases the compatibility 
with the OpenCV cascades (when they use tilted features, eg mouth cascade)


####Detector Methods

__constructor()__
```php
new detector($haardata);
```

__Explanation of parameters__

* _$haardata_ : The actual haardata (as generated by haartophp tool), this is specific per feature, openCV haar data can be used.



__clearCache()__
```php
$detector->clearCache();
```

Clear any cached image data and haardata in case space is an issue. Use image method and cascade method (see below) to re-set image and haar data



__cascade()__
```php
$detector->cascade($haardata);
```

Allow to use same detector (with its cached image data), to detect different feature on same image, by using another cascade. This way any image pre-processing is done only once

__Explanation of parameters__

* _$haardata_ : The actual haardata (as generated by haartojs tool), this is specific per feature, openCV haar data can be used.



__image()__
```php
$detector->image($GDImage, $scale);
```

__Explanation of parameters__

* _$GDImage_ : an actual GD Image object.
* _$scale_ : The percent of scaling from the original image, so detection proceeds faster on a smaller image (default __0.5__ ). __NOTE__ scaling might alter the detection results sometimes, if having problems opt towards 1 (slower)



__selection()__
```php
$detector->selection('auto'|array|feature|$x [,$y, $width, $height]);
```

Allow to set a custom region in the image to confine the detection process only in that region (eg detect nose while face already detected)

__Explanation of parameters__

* _1st parameter_ : This can be the string 'auto' which sets the whole image as the selection, or an array ie: array('x'=>10, 'y'=>'auto', 'width'=>100, 'height'=>'auto'} (every param set as 'auto' will take the default image value) or a detection rectangle/feature, or a x coordinate (along with rest coordinates).
* _$y_ : (Optional) the selection start y coordinate, can be an actual value or 'auto' ($y=0)
* _$width_ : (Optional) the selection width, can be an actual value or 'auto' ($width=image.width)
* _$height_ : (Optional) the selection height, can be an actual value or 'auto' ($height=image.height)

The actual selection rectangle/feature is available as $this->Selection or $detector->Selection



__cannyThreshold()__
```php
$detector->cannyThreshold(array('low'=> lowThreshold, 'high'=> highThreshold));
```

Set the thresholds when Canny Pruning is used, for extra fine-tuning. 
Canny Pruning detects the number/density of edges in a given region. A region with too few or too many edges is unlikely to be a feature. 
Default values work fine in most cases, however depending on image size and the specific feature, some fine tuning could be needed

__Explanation of parameters__

* _low_ : (Optional) The low threshold (default __20__ ).
* _high_ : (Optional) The high threshold (default __100__ ).




__detect()__
```php
$detector->detect($baseScale, $scale_inc, $increment, $min_neighbors, $doCannyPruning);
```

__Explanation of parameters__ ([JViolaJones Parameters](http://code.google.com/p/jviolajones/wiki/Parameters))

* _$baseScale_ : The initial ratio between the window size and the Haar classifier size (default __1__ ).
* _$scale_inc_ : The scale increment of the window size, at each step (default __1.25__ ).
* _$increment_ : The shift of the window at each sub-step, in terms of percentage of the window size (default __0.1__ ).
* _$min_neighbors_ : The minimum numbers of similar rectangles needed for the region to be considered as a feature (avoid noise) (default __1__ )
* _$doCannyPruning_ : enable Canny Pruning to pre-detect regions unlikely to contain features, in order to speed up the execution (optional default __true__ ). 


__Examples included with face detection__


*HAARPHP* is also part of PHP classes http://www.phpclasses.org/package/7393-PHP-Detect-features-on-images-such-as-faces-or-mouths.html


###Where to find Haar Cascades xml files to use for feature detection

* [OpenCV](http://opencv.org/)
* [This resource](http://alereimondo.no-ip.org/OpenCV/34)
* search the web :)
* [Train your own](http://docs.opencv.org/doc/user_guide/ug_traincascade.html) with a little extra help [here](http://note.sonots.com/SciSoftware/haartraining.html) and [here](http://coding-robin.de/2013/07/22/train-your-own-opencv-haar-classifier.html)


####TODO
- [ ] keep up with the changes in openCV cascades xml format (will try)


####ChangeLog

__0.4__
* refactor code (make smaller)
* add clearCache method, to delete any stored/cached image data in the detector (in case space is an issue)
* add the tilted feature (Lienhart et al, extension)
* make new haartophp tool, output format changed, __make sure to re-convert your .php haar cascades!!__
* tidy up the repo
* fix some typos, edits
* project stopped


__0.3__
* add new methods (_selection_ , _cascade_ , _cannyThreshold_ )
* use fixed-point arithmetic if possible (eg gray-scale, canny computation)
* optimize array indexing, remove unnecessary multiplications
* reduce unnecessary loops, inline code instead of method calling for speed
* rewrite _merge_ method (features might be slightly different now)
* features are now generic classes not arrays
* code refactor/fixes
* update readme, add method documentation


__0.2__
* add haartophp tool in php (all-php solution)
* optimize array operations, refactor, etc..


__0.1__
* initial release


*URL* [Nikos Web Development](http://nikos-web-development.netai.net/ "Nikos Web Development")  
*URL* [Haar.php blog post](http://nikos-web-development.netai.net/blog/haarphp-feature-detection-with-haar-cascades-in-php/ "Haar.php blog post")  
*URL* [WorkingClassCode](http://workingclasscode.uphero.com/ "Working Class Code")  
