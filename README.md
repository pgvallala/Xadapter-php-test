# Xadapter-php-challenge

This code can be used to  fetch orders based on a specified time span (Fetching  "Jun 2017" orders in this example) from a demo woocommerce store http://insilla.vooforce.com/ using WooCommerce API.


```php
<?php

    // Helper function to parse http response headers
    function parse_pages($content){
        if( preg_match_all("/X-WP-TotalPages: ([0-9]*)/",$content, $match ) ){
            return $match[1][0];
        }
        return "-1";
    }
 
    // Insert api access key and secret here
    $username = '<key here>'; 
    $password = '<secret here>';
  
    // These can be augmented in order to change time span
    $from_date = "2017-05-31T23:59:59"
    $to_date = "2017-07-01T00:00:00"

    $url = "https://insilla.vooforce.com/wp-json/wc/v2/orders?after=$from_date&before=$to_date";

    $options = array(
        CURLOPT_URL => "$url",
        CURLOPT_HTTPAUTH => CURLAUTH_BASIC,
        CURLOPT_USERPWD => "$username:$password",
        CURLOPT_SSL_VERIFYPEER => false,
        CURLOPT_RETURNTRANSFER => true
    );

    $header_only_options = array(
        CURLOPT_HEADER => true,
        CURLOPT_NOBODY => true
    );

    $ch = curl_init();

    curl_setopt_array($ch, $options + $header_only_options);
    $headers = curl_exec($ch);
    $pages = parse_pages($headers);
    
    curl_close($ch);

    $ch = curl_init();
    
    curl_setopt_array($ch, $options);

    print("Making $pages pagination requests..");

    for ($page = 1; $page <= $pages; $page++) {
        curl_setopt($ch, CURLOPT_URL, "$url&page=$page");
        $content = json_decode(curl_exec($ch));
        echo json_encode($content, JSON_PRETTY_PRINT);
        echo "\n\n";
    }

    curl_close($ch);

    ?>
```
