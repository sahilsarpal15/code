<?php
//die(normalize(""));
$files = scandir('rawData/');

array_shift($files);
array_shift($files);

$ratings = array();

foreach ($files as $file) {

	$data = parseFile($file);
	saveFile($file, $data);	
	
}

function saveFile($fileName, $data){
	file_put_contents('generatedData/'.$fileName, $data);
}

function parseFile($file){
	echo "parsing: $file".PHP_EOL;
	$file =	"rawData/".$file;
	$data = file_get_contents($file);

	$data = json_decode($data, true);
	
	$title = @$data['title'];
	$year = (int)@$data['year'];
	$rating = (float)@$data['rating'];
	
	$genres = @array_values($data['genres']);
	$normalizedGenres = [];
	foreach ($genres as $genre) {
		$normalizedGenres[] = normalize($genre);
	}
	$genres = $normalizedGenres;

	$directors = @array_values($data['directors']);
	$normalizedDirectores = [];
	foreach ($directors as $director) {
		$normalizedDirectores[] = normalize($director);
	}
	$directors = $normalizedDirectores;
	
	$writers = @array_values($data['writers']);
	$normalizedWriters = [];
	foreach ($writers as $writer) {
		$normalizedWriters[] = normalize($writer);
	}
	$writers = $normalizedWriters;
	
	$stars = @array_values($data['stars']);
	$normalizedStars = [];
	foreach ($stars as $star) {
		$normalizedStars[] = normalize($star);
	}
	$stars = $normalizedStars;
	
	$producers = @array_values($data['producers']);
	$normalizedProducers = [];
	foreach ($producers as $producer) {
		$normalizedProducers[] = normalize($producer);
	}
	$producers = $normalizedProducers;	
	
	$musicians = @array_values($data['musicians']);
	$normalizedMusicians = [];
	foreach ($musicians as $musician) {
		$normalizedMusicians[] = normalize($musician);
	}
	$musicians = $normalizedMusicians;	
	
	$cinematographers = @array_values($data['cinematographers']);
	$normalizedCinematographers = [];
	foreach ($cinematographers as $cinematographer) {
		$normalizedCinematographers[] = normalize($cinematographer);
	}
	$cinematographers = $normalizedCinematographers;	
	
	$editors = @array_values($data['editors']);
	$normalizedEditor= [];
	foreach ($editors as $editor) {
		$normalizedEditor[] = normalize($editor);
	}
	$editors = $normalizedEditor;
	
	$mpaa_rating = @$data['mpaa_rating'];
	if($mpaa_rating === "PG"){
		$mpaa_rating = 0;
	}else if($mpaa_rating === "R"){
		$mpaa_rating = 1;
	}else if($mpaa_rating === "PG-13"){
		$mpaa_rating = 2;
	}else if($mpaa_rating === "NC-17"){
		$mpaa_rating = 3;
	}else {
		$mpaa_rating = null;
	}

	$runtime = (int)@$data['runtime'];
	$is_in_top_250 = (int)@$data['top_250'];
	$has_won_oscars = @$data['oscars'] == null?0:1;
	$votes = (int)str_replace(",", "", @$data['votes']);
	$language = @$data['language'][0];
	$country = @$data['country'][0];
	$recommendations = @$data['recommendations'];

	$data = array(
		"title" => $title ,
		"year" => $year,
		"rating" => $rating,
		"genres" => $genres,
		"directors" => $directors,
		"writers" => $writers,
		"stars" => $stars,
		"producers" => $producers,
		"musicians" => $musicians,
		"cinematographers" => $cinematographers,
		"editors" => $editors,
		"mpaa_rating" => $mpaa_rating,
		"runtime" => $runtime,
		"is_in_top_250" => $is_in_top_250,
		"has_won_oscars" => $has_won_oscars,
		"votes" => $votes,
		"language" => $language,
		"country" => $country,
		"recommendations" => $recommendations);

	$data = json_encode($data, JSON_UNESCAPED_UNICODE);

	// echo $data;
	// readline("");
	return $data;
}

function normalize($text){
	
	/*
	* remove spaces from starting and ending
	* removes any consective spaces
	* converts any letter other than alphanumeric to "-"
	*/
	
	if($text === null)
		return null;
	$text = trim($text);

	$text = htmlspecialchars_decode($text);
	$text = html_entity_decode($text);
	$text = preg_replace('/\s+/', ' ', $text);
	$text =  preg_replace("/[^[:alnum:]]/u", '-', $text);
	$text = preg_replace('/--+/', '-', $text);
	$text = strtolower($text);
	$text = str_replace('&#x27;',"'", $text);
	return $text;
}


/*

title
year 
rating
genres
directors
writers
stars
producers
musicians
cinematographers
editors
mpaa_rating
runtime
is_in_top_250
has_won_oscars
votes
language
country


ratings
---------
-1 if false
PG = 0
R = 1
PG-13 = 2
NC-17 = 3
