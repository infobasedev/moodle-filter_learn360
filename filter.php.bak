<?php
// This file is part of Moodle - http://moodle.org/
//
// Moodle is free software: you can redistribute it and/or modify
// it under the terms of the GNU General Public License as published by
// the Free Software Foundation, either version 3 of the License, or
// (at your option) any later version.
//
// Moodle is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License
// along with Moodle.  If not, see <http://www.gnu.org/licenses/>.


/**
 * Learn360 Video filter plugin.
 * @Author    Pramod Ubbala (AGS -> Infobase)
 * @package    filter_learn360
 * @license    http://www.gnu.org/copyleft/gpl.html GNU GPL v3 or later
 */

defined('MOODLE_INTERNAL') || die();

class filter_learn360 extends moodle_text_filter {
  
  private $embedmarkers;

  public function filter($text, array $options = array()) {
    global $CFG, $PAGE;

	$dom = new DOMDocument();
	$dom->loadHTML($text);
	$el = $dom->getElementsByTagName('a');

	foreach($el as $node)
	{
		$a = $dom->saveHTML($node);

		preg_match("/<a\s+(?:[^>]*?\s+)?href=\"([^\"]*)\"/", $a, $b64text);

		try
		{
			$obj = json_encode(base64_decode($b64text[1]),true);
			 $results = print_r($obj, true);
			$temp=json_decode($results, true);

			$newtext = json_decode($temp,true);
		}
		catch(Exception $e)
		{
			continue;
		}

		
		//if our url doesn't need to be encoded, set it to the OG
		if($newtext['url'] == '')
		{
			$newtext['url'] = $b64text[1];
		}

		//only do this if it's our url
		if(stripos($newtext['url'], '.infobase.com') !== false && stripos($newtext['url'], '/OnDemandEmbed') !== false)
		{
			$text=str_replace($b64text[1],$newtext['url'],$text);

			$a = str_replace($b64text[1],$newtext['url'],$a);

			//get the substring of the url with moodle/ so we can decode it to match the actual text
			if(stripos($newtext['url'], 'moodle/') !== false)
			{
				$moodleText = substr($newtext['url'], stripos($newtext['url'], 'moodle/') + 7, strlen($newtext['url']) - stripos($newtext['url'], 'moodle/') + 7);

				$a = str_replace("moodle/". $moodleText, "moodle/". urldecode($moodleText), $a);
			}

			if (!is_string($text) or empty($text)) {
				// non string data can not be filtered anyway
				return $text;
			}

			if (stripos($text, '</a>') === false) {
			// Performance shortcut - if not </a> tag, nothing can match.
				return $text;
			}

			$this->embedmarkers = 'lti\.films\.com|localhost|\.mp4';

			$this->trusted = !empty($options['noclean']) or !empty($CFG->allowobjectembed);

			$width =  435;
			$height = 382;


			if(strpos($newtext['url'], '&w=') !== false)
			{
				$dims = substr($newtext['url'], strpos($newtext['url'], '&w='), strlen($newtext['url']) - strpos($newtext['url'], '&w='));

				$width = substr($dims, strpos($dims, '&w=') + 3, strpos($dims, '&h')-3) + 20;
				$height = substr($dims, strpos($dims, '&h=') + 3, strlen($dims) -strpos($dims, '&h=') + 3) + 50;

			}

			if(strpos($newtext['url'], '.infobase.com') !== false) {

				$iframeText = '<iframe src="' . $newtext['url'] . '" frameborder="0" style="width: ' . $width . 'px;height:' . $height . 'px;" allowfullscreen></iframe>';

				$text = str_replace($a, $iframeText, $text);

			}

			if (empty($newtext) or $newtext === $text) {
				// error or not filtered
				continue;
			}		
	}

    
	return $text;	
  }

  function encodeURLs($text, array $options = array())
	{
		if (!is_string($text) or empty($text)) {
		// non string data can not be filtered anyway
			return $text;
		}

		if (stripos($text, '</a>') === false) {
		// Performance shortcut - if not </a> tag, nothing can match.
			return $text;
		}

		$this->embedmarkers = 'lti\.films\.com|localhost|\.mp4';

		$this->trusted = !empty($options['noclean']) or !empty($CFG->allowobjectembed);

		preg_match("/<a\s+(?:[^>]*?\s+)?href=\"([^\"]*)\">([^\"<]*)<\/a>/", $text, $newtext);

		$width =  435;
		$height = 382;

		if(strpos($newtext[1], '&w=') !== false)
		{
			$dims = substr($newtext[1], strpos($newtext[1], '&w='), strlen($newtext[1]) - strpos($newtext[1], '&w='));

			$width = substr($dims, strpos($dims, '&w=') + 3, strpos($dims, '&h')-3) + 20;
			$height = substr($dims, strpos($dims, '&h=') + 3, strlen($dims) -strpos($dims, '&h=') + 3) + 50;

		}

		if(strpos($newtext[1], '.infobase.com') !== false) {

			$iframeText = '<iframe src="' . $newtext[1] . '" frameborder="0" style="width: ' . $width . 'px;height:' . $height . 'px;" allowfullscreen></iframe>';

			$text = str_replace($newtext[0], $iframeText, $text);

		}

		if (empty($newtext) or $newtext === $text) {
			// error or not filtered
			return $text;
		}

		return $text;
	}
  
  /**
     * Replace link with embedded content, if supported.
     *
     * @param array $matches
     * @return string
     */
    private function callback(array $matches) {
        global $CFG, $PAGE;
        
        // Get name.
        $name = trim($matches[2]);
        if (empty($name) or strpos($name, 'http') === 0) {
            $name = ''; // Use default name.
        }

        $width =  364;
        $height = 300;
       
		$result = $matches[1];
		
        // If something was embedded, return it, otherwise return original.
        if ($result !== '') {
            return $result;
        } else {
            //return $obj[0];
            return $matches[0];
        }
    }

}
