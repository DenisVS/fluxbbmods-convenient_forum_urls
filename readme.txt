##
##
##        Mod title:  Convenient Forum URL's 
##
##      Mod version:  1.0
##  Works on FluxBB:  1.5.10
##     Release date:  2017-04-16
##           Author:  DenisVS (deniswebcomm@gmail.com)
##     Based on mod:  Convenient Forum URL's for PunBB by hcs (hcs@mail.ru)
##
##      Description:  This mod converted links to toipic in post when posting to bb-code [url][/url] 
##		      Topic subject will be added automatically 
##		      Example: when you posting message - http://myforum/viewtopic.php?id=21
##		      mod this replacement with: [url=http://myforum/viewtopic.php?id=21]Topic_subject_here[/url]
##
##   Affected files:  post.php
##                    include/functions.php
##
##       Affects DB:  no
##
##
##       DISCLAIMER:  Please note that "mods" are not officially supported by
##                    FluxBB. Installation of this modification is done at your
##                    own risk. Backup your forum database and any and all
##                    applicable files before proceeding.
##
##

 
#
#---------[ 1. OPEN ]---------------------------------------
#

post.php
 
 
#---------[ 2. FIND (line  ~ 136)   ]-----------------------
#

// Validate BBCode syntax
 
#
#---------[ 3. BEFORE ADD ]----------------------------------
#

	replace_url_friendly($message);
 
 
 
#
#---------[ 4. OPEN ]---------------------------------------
#

edit.php
 
 
#---------[ 5. FIND (line  ~ 89)   ]-----------------------
#

// Validate BBCode syntax
 
#
#---------[ 6. BEFORE ADD ]----------------------------------
#

	replace_url_friendly($message);
 
 
#
#---------[ 7. OPEN (Only if you use the "Ajax post edit" mod!) ]-----
#

include/ajax_post_edit/edit.php
 
#
#--[ 8. FIND (line  ~ 100. Only if you use the "Ajax post edit" mod!) ]-
#

// Validate BBCode syntax
 
#
#---------[ 9. BEFORE ADD (Only if you use the "Ajax post edit" mod!)]--
#

	replace_url_friendly($message);
 
#
#---------[ 10. OPEN ]----------------------------------------
#

include/functions.php
 
#
#---------[ 11. FIND  (line  ~ 1667) ]-------------------------
#

//
// Unset any variables instantiated as a result of register_globals being enabled
//
 
#
#---------[ 12. BEFORE ADD ]----------------------------------
#

///hcs friendly url replacment function  
function replace_url_friendly(&$text)
{
	global $db, $pun_config;
	$url = str_replace("/", "\/", str_replace(".", "\.", $pun_config['o_base_url']."/viewtopic.php\?"));
	$pattern = "/(?<=^|\s)".$url."pid=([0-9]+)#p[0-9]+\b/";
	if (preg_match_all($pattern, $text, $regs, PREG_SET_ORDER)) {
		foreach ($regs as $pid) {
			$result = $db->query('SELECT t.subject FROM '.$db->prefix.'posts AS p INNER JOIN '.$db->prefix.'topics AS t ON t.id = p.topic_id WHERE p.id='.$pid[1]) or error('Unable to fetch topic subject', __FILE__, __LINE__, $db->error());
			if ($result) {
				$topic_subject = $db->fetch_assoc($result);
				$pattern = "/(?<=^|\s)".str_replace("/", "\/", str_replace("?", "\?", str_replace(".", "\.", $pid[0])))."\b/";
				$text=preg_replace ($pattern, "[url=".$pid[0]."]".$topic_subject['subject']."[/url]", $text,1);
			} 
		}
	}
	$pattern = "/(?<=^|\s)".$url."id=([0-9]+)\b/";
	if (preg_match_all($pattern, $text, $regs, PREG_SET_ORDER)) {
		foreach ($regs as $pid) {
			$result = $db->query('SELECT subject FROM '.$db->prefix.'topics WHERE id='.$pid[1]) or error('Unable to fetch topic subject', __FILE__, __LINE__, $db->error());
			if ($result) {
				$topic_subject = $db->fetch_assoc($result);
				$pattern = "/(?<=^|\s)".str_replace("/", "\/", str_replace("?", "\?", str_replace(".", "\.", $pid[0])))."\b/";
				$text=preg_replace ($pattern, "[url=".$pid[0]."]".$topic_subject['subject']."[/url]", $text,1);
			} 
		}
	}	
}
 
 
#
#---------[ 13. SAVE/UPLOAD ]------------------------------
#