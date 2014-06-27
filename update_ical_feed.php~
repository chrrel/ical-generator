<?php
/*
iCalendar Specification Excerpts:  http://www.kanzaki.com/docs/ical/ 
*/

/* ical_time returns the time in the format Thhmmss (T is a seperator, not a placeholder), $time has to be in hh:mm format, if $time is empty the return value will be T000000*/
function ical_time($time) 
{
	if($time)
	{
		$time = explode(":" , $time );
			$time[0] = $time[0] ; // UTC-time: -2 hours
		$time = $time[0].$time[1]."00" ; // UTC-time: add a "Z"
	}
	else
	{
		$time = "000000"; 
	}

	return "T" .$time;
}

/*ical_clean_title removes HTML numbers and special characters*/
function ical_clean_title($title)
{
	$title = preg_replace('~&#x([0-9a-f]+);~ei', '', $title); 
	$title = preg_replace('~&#([0-9]+);~e', '', $title);

	return $title;
}

/*write_events is a helper function for get_data and provides the actual content.*/
function write_events($id, $i, $title, $date, $enddate, $time, $endtime, $all_day)
{
	$output .= "BEGIN:VEVENT\r\n";
	$output .="SUMMARY:".ical_clean_title($title);
		if($time  && !$endtime) $output.=" (".$time .")"; 	
		if($time && $endtime ) $output.=" (".$time ." bis " .$endtime.")"; 	

	$output.="\r\n".
	"CLASS:PUBLIC\r\n".
	"CATEGORIES:YOURCALENDAR\r\n";
	$output .= 'DTSTART:' .$date .ical_time($time) ."\r\n";	
	
	if($enddate)
	{
		if($all_day) // enddate is defined && event is all day ->  set endtime to 23:59, otherwise it would be at 00:00
			$output .= 'DTEND:'.$enddate.ical_time("23:59") ."\r\n"; // 
		else
			$output .= 'DTEND:'.$enddate.ical_time($endtime) ."\r\n"; 
	}
	else
	{
		if($all_day) // enddate is not defined && event is all day -> set endtime to 23:59, otherwise it would be at 00:00
			$output .= 'DTEND:'.$date.ical_time("23:59") ."\r\n"; // 
		else
			$output .= 'DTEND:'.$date.ical_time($endtime) ."\r\n"; 
	}


	$output .= 'UID:'. $i .'@YOURCALENDAR'."\r\n";
	$output .= "END:VEVENT\r\n";
	
	return $output;
}

/* get_data returns the queried Wordpress data in $output.*/
function get_data()
{
	/* Connect to the Wordpress loop */
	define('WP_USE_THEMES', false); 
	require('../../wp-blog-header.php');

	$i=0; // counter used for event id
	$args = array( 'post_type' => 'termine','posts_per_page' => -1, 'meta_key' => 'Datum', 'orderby' => 'meta_value', 'order' => 'ASC' );
		// CPT ordered by date
	$loop = new WP_Query( $args);

	$maxdate = strtotime("-6 months"); // date 6 months ago
	$maxdate = date("Ymd", $maxdate);

	while ($loop->have_posts() ) 
	{
	    $loop->the_post();

	    $id = get_the_ID();
	    $date = get_post_meta($id, 'Datum', true );
	    $enddate =get_post_meta($id, 'Enddatum', true);
    	    $time = get_post_meta($id, 'Uhrzeit', true );
	    $endtime = get_post_meta($id, 'Enduhrzeit', true);
	    $title = get_the_title('','');
	    $all_day = get_post_meta($id, 'ganztaegig', true ); //checkbox
	    $all_day = $all_day[0]; //if an event is all day, the value will be 1

	    if($enddate>= $maxdate || $date>=$maxdate) // Only show events that are yet to come or took place after maxdate
	    {
		$output .= write_events($id, $i, $title, $date, $enddate, $time, $endtime, $all_day);
	    }
	$i++;
	}
 	wp_reset_postdata(); 

	return $output;
}

/*write_feed creates the ical file*/
function write_feed($prodid, $calname, $feedname)
{
	$feed = "BEGIN:VCALENDAR\r\n".
        	"VERSION:2.0\r\n".
        	"METHOD:PUBLISH\r\n".
		"PRODID:" .$prodid ."\r\n".
		"X-WR-CALNAME:" .$calname ."\r\n";
	$feed .= get_data();
	$feed .= "END:VCALENDAR\r\n";

	if (!$handle = fopen($feedname, "wb"))
	{
		print "Can not open the file $feedname";
		exit;
	}
	if (!fwrite($handle, $feed))
	{
		print "Can not write in the file $feedname";
		exit;
	}
	fclose($handle);
}

/* Call write_feed(). No other function has to be called.*/ 
write_feed("-//YOURCALENDAR//NONSGML CALENDARNAME//DE", "CALENDARNAME", "filename.ics");
?>
