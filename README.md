# PHP NSFW Classifier
A **single PHP Class** to classify **NSFW contents** (i.e. Users image uploads) via the PixLab Machine Vision APIs - https://pixlab.io/cmd?id=nsfw
## Requirements
PixLab PHP NSFW Classify Class works with PHP 5.3, 5.4, 5.5, 5.6, 7.0, 7.1, 7.2, and HHVM.
## Install
To install PHP NSFW Class, simply:
```shell
$ composer require symisc/symisc/pixlab-php-nsfw
```
Or simply drop ***pixlab.php*** on your source tree and you're done!
## Usage Example
```php
<?php
require_once "pixlab.php";

# Target Image: Change to any link (Possibly adult) you want or switch to POST 
# if you want to upload your image directly. 
$img = 'https://i.redd.it/oetdn9wc13by.jpg';

# Your PixLab key - Get one from https://pixlab.io/dashboard
$key = 'My_Pixlab_Key';

# Blur an image based on its NSFW score
$pix = new Pixlab($key);
/* Invoke NSFW */
if( !$pix->get('nsfw',['img' => $img]) ){
	echo $pix->get_error_message();
	die;
}
/* Grab the NSFW score */
$score = $pix->json->score;
if( $score < 0.5 ){
	echo "No adult content were detected on this picture\n";
}else{
	echo "Censuring NSFW picture...\n";
	/* Call blur with the highest possible radius and sigma */
	if( !$pix->get('blur',array('img' => $img,'rad' => 50,'sig' =>30)) ){
		echo $pix->get_error_message();
	}else{
		echo "Censured Picture: ".$pix->json->link."\n";
	}
}
?>
```
## PixLab Resources
[PixLab Resources &amp; Sample Set](https://pixlab.io/examples). The Sample Set are practical usage, working code available in various programming languages intended to familiarize the reader with the PixLab API.
For an introduction course, please refer to:
* [The PixLab API in 5 minutes or less](https://pixlab.io/start).
* [List of API endpoints](https://pixlab.io/api).
* [The PixLab API Reference Guide](https://pixlab.io/cmdls).
* [The PixLab Sample Set](https://pixlab.io/examples).

You’re welcome to copy/paste and run these examples to see the API in action.
