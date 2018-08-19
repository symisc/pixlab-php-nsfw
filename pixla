<?php
/* 
 * PixLab Official PHP client - https://pixlab.io. 
 * Copyright (C) 2017 Symisc Systems, SUARL http://symisc.net.
 * License: BSD-2 Clause
 */
 class Pixlab {
	private $key = null;   /* The PixLab Key */
	public $status = 200;  /* HTTP status code */
	public $json = null;   /* Decoded JSON response from the Pixlab API server */
	public $raw_json = null; /* Raw JSON response */
	public $blob =  null;  /* Raw (Binary image content) response from the Pixlab API server */
	public $mime = '';     /* PixLab API Server MIME type response */
	public $error = '';    /* Error message if $status != 200 */
	public $scheme = 'https://'; /* Default to HTTPS but if you are calling from a trusted network consider using clear http:// for performance reason */
	
	public function __construct($key) {
		$this->key = $key;
	}
	public function get_status(){
		return $this->status;
	}
	public function get_blob(){
		return $this->blob;
	}
	public function switch_to_http(){
		$this->scheme = 'http://';
	}
	public function get_decoded_json(){
		return $this->json;
	}
	public function get_raw_json(){
		return $this->raw_json;
	}
	public function get_mime(){
		return $this->mime;
	}
	public function get_error_message(){
		return $this->error;
	}
	public function get($cmd,$param = []) {
		if(!$this->key || strlen($this->key) < 15 ){
			$this->status = 401; /* Unauthorized */
			$this->error = 'Missing/Invalid PixLab API Key';
			return false;
		}
		$cmd = basename(trim($cmd," \t/"));
		/* Build the query first */
		$param['key'] = $this->key;
		$request = $this->scheme."api.pixlab.io/$cmd?".http_build_query($param);
		/* Make the request now */
		$curl = curl_init($request);
		curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
		$res = curl_exec($curl);
		if ($res === false) {
			$this->status = curl_getinfo($curl, CURLINFO_HTTP_CODE);
			$this->error = curl_error($curl);
			curl_close($curl);
			return false;
		}
		/* Get the response mime type */
		$this->mime = trim(curl_getinfo($curl, CURLINFO_CONTENT_TYPE));
		/* Close that connection */
		curl_close($curl);
		if( $this->mime == 'application/json'){
			$this->raw_json = $res;
			$this->json = json_decode($res);
			$this->status = $this->json->status;
			if( $this->status != 200){
				$this->error = $this->json->error;
				return false;
			}
		}else{
			/* Successful blob response since error are returned in JSON format */
			$this->blob = $res;
		}
		/* All done */
		return true;
	}
	public function post($cmd,$param = [],$file_upload = false) {
		if(!$this->key || strlen($this->key) < 15 ){
			$this->status = 401; /* Unauthorized */
			$this->error = 'Missing/Invalid PixLab API Key';
			return false;
		}
		$cmd = basename(trim($cmd," \t/"));
		$curl = curl_init($this->scheme."api.pixlab.io/$cmd?");
		curl_setopt($curl, CURLOPT_POST, true);
		/* Build the query first */
		$param['key'] = $this->key;
		if( !$file_upload ){
			/* Default to JSON form */
			$request = json_encode($param);
			curl_setopt($curl, CURLOPT_HTTPHEADER, array('Content-Type: application/json'));
		}else{
			$file_upload = realpath($file_upload);
			if(!is_readable($file_upload) ){
				$this->status = 404; /* Not found  */
				$this->error = "Target file '$file_upload' not found or is unreadable";
				return false;
			}
			 $cFile = curl_file_create($file_upload);
			/* Standard multi-part/form-data upload */
			$param['file'] = $cFile;
			$request = $param;
		}
		/* Make the request now */
		curl_setopt($curl, CURLOPT_POSTFIELDS, $request); 
		curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
		$res = curl_exec($curl);
		if ($res === false) {
			$this->status = curl_getinfo($curl, CURLINFO_HTTP_CODE);
			$this->error = curl_error($curl);
			curl_close($curl);
			return false;
		}
		/* Get the response mime type */
		$this->mime = trim(curl_getinfo($curl, CURLINFO_CONTENT_TYPE));
		/* Close that connection */
		curl_close($curl);
		if( $this->mime == 'application/json'){
			$this->raw_json = $res;
			$this->json = json_decode($res);
			$this->status = $this->json->status;
			if( $this->status != 200){
				$this->error = $this->json->error;
				return false;
			}
		}else{
			/* Successful blob response since error are returned in JSON format */
			$this->blob = $res;
		}
		/* All done */
		return true;
	}
 }
?>
