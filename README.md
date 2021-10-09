# sharath

//CREATING USER BY ID://
URL url=new URL(https://users);
HttpURL connection con=(HttpURLConnection)url.openConnection();
con.setRequestMethod(“POST”);
con.setRequestProperty(“Content-Type”,”application/json”;utf-8”);
con.setRequestProperty(“Accept”, ”application/json”);
con.setDoOutput(true);
String jsonInputString=”{“name”:”Sharath”,email-id:msschandra2002@gmail.com,Id:”19BCE0316”,password:”1234”}”
try(OutputStream os = con.getOutputStream()) {
    byte[] input = jsonInputString.getBytes("utf-8");
    os.write(input, 0, input.length);			
}
try(BufferedReader k = new BufferedReader(
  new InputStreamReader(con.getInputStream(), "utf-8"))) {
    StringBuilder response = new StringBuilder();
    String responseLine = null;
    while ((responseLine = k.readLine()) != null) {
        response.append(responseLine.trim());
    }
    System.out.println(response.toString());
}
//GET USER BY ID://
$instagram = $modules->get('InstagramBasicDisplayApi');
$Id = $instagram->getId('username');
Get/users?/id={id}
Get/users?/email={email}
Get/users?/Name={Name};
Get/users?/password={password};
//POST-PHOTOS IN INSTAGRAM ://
function SendRequest($url, $post, $post_data, $user_agent, $cookies) {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, 'https://posts);
    curl_setopt($ch, CURLOPT_USERAGENT, $user_agent);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);

    if($post) {
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
    }

    if($cookies) {
        curl_setopt($ch, CURLOPT_COOKIEFILE, 'cookies.txt');            
    } else {
        curl_setopt($ch, CURLOPT_COOKIEJAR, 'cookies.txt');
    }

    $response = curl_exec($ch);
    $http = curl_getinfo($ch, CURLINFO_HTTP_CODE);
    curl_close($ch);

   return array($http, $response);
}

function GenerateGuid() {
     return sprintf('%04x%04x-%04x-%04x-%04x-%04x%04x%04x', 
            mt_rand(0, 12356), 
            mt_rand(0, 12356), 
            mt_rand(0, 12356), 
            mt_rand(16384, 20479), 
            mt_rand(32768, 49151), 
            mt_rand(0, 12356), 
            mt_rand(0, 12356), 
            mt_rand(0, 12356));
}

function GenerateUserAgent() {  
     $resolutions = array('720x1280', '320x480', '480x800', '1024x768', '1280x720', '768x1024', '480x320');
     $versions = array('GT-N7000', 'SM-N9000', 'GT-I9220', 'GT-I9100');
     $dpis = array('120', '160', '320', '240');

     $ver = $versions[array_rand($versions)];
     $dpi = $dpis[array_rand($dpis)];
     $res = $resolutions[array_rand($resolutions)];

     return 'Instagram 4.'.mt_rand(1,2).'.'.mt_rand(0,2).' Android ('.mt_rand(10,11).'/'.mt_rand(1,3).'.'.mt_rand(3,5).'.'.mt_rand(0,5).'; '.$dpi.'; '.$res.'; samsung; '.$ver.'; '.$ver.'; smdkc210; en_US)';
 }

function GenerateSignature($data) {
     return hash_hmac('sha256', $data, 'b4a23f5e39b5929e0666ac5de94c89d1618a2916');
}

function GetPostData($file_name) {
    if(!$file_name) {
        echo "The image doesn't exist ".$file_name;
    } else {
        $post_data = array('device_timestamp' => time(), 
                        'photo' => '@'.$file_name);
        return $post_data;
    }
}

// Set the username and password of the account that you wish to post a photo to
$username = 'ig_username';
$password = 'ig_password';

// Set the path to the file that you wish to post.
// This must be jpeg format and it must be a perfect square
$filename = 'pictures/test.jpg';

// Set the caption for the photo
$caption = "Test caption";

// Define the user agent
$agent = GenerateUserAgent();

// Define the GuID
$guid = GenerateGuid();

// Set the devide ID
$device_id = "android-".$guid;$data ='{"device_id":"'.$device_id.'","guid":"'.$guid.'","username":"'.$username.'","password":"'.$password.'","Content-Type":"application/x-www-form-urlencoded; charset=UTF-8"}';
$sig = GenerateSignature($data);
$data = 'signed_body='.$sig.'.'.urlencode($data).'&ig_sig_key_version=4';
$login = SendRequest('accounts/login/', true, $data, $agent, false);

if(strpos($login[1], "Sorry, an error occurred in  processing this request.")) {
    echo "Request failed, there's a chance that this proxy/ip is blocked";
} else {            
    if(empty($login[1])) {
        echo "Empty response received from the server while trying to login";
    } else {            
        // Decode the array that is returned
        $obj = @json_decode($login[1], true);

        if(empty($obj)) {
            echo "Could not decode the response: ".$body;
        } else {
            // Post the picture
            $data = GetPostData($filename);
            $post = SendRequest('media/upload/', true, $data, $agent, true);    

            if(empty($post[1])) {
                 echo "Empty response received from the server while trying to post the image";
            } else {
                // Decode the response 
                $obj = @json_decode($post[1], true);

                if(empty($obj)) {
                    echo "Could not decode the response";
                } else {
                    $status = $obj['status'];

                    if($status == 'ok') {
                        // Remove and line breaks from the caption
                        $caption = preg_replace("/\r|\n/", "", $caption);

                        $media_id = $obj['media_id'];
                        $device_id = "android-".$guid;
                        $data = '{"device_id":"'.$device_id.'","guid":"'.$guid.'","media_id":"'.$media_id.'","caption":"'.trim($caption).'","device_timestamp":"'.time().'","source_type":"5","filter_type":"0","extra":"{}","Content-Type":"application/x-www-form-urlencoded; charset=UTF-8"}';   
                        $sig = GenerateSignature($data);
                        $new_data = 'signed_body='.$sig.'.'.urlencode($data).'&ig_sig_key_version=4';
                      
                      $conf = SendRequest('media/configure/', true, $new_data, $agent, true);

                       if(empty($conf[1])) {
                           echo "Empty response received from the server while trying to configure the image";
                       } else {
                           if(strpos($conf[1], "login_required")) {
                                echo "You are not logged in. There's a chance that the account is banned";
                            } else {
                                $obj = @json_decode($conf[1], true);
                                $status = $obj['status'];

                                if($status != 'fail') {
                                    echo "SUCCESS";
                                } else {
                                    echo 'FAIL';
                                }
                            }
                        }
                    } else {
                        echo "STATUS ISN'T OKAY";
                    }
                }
            }
        }
    }
}
//Get post by Id//
function add_column( $columns ){
	$columns['post_id_clmn'] = 'ID'; // $columns['Column ID'] = 'Column Title';
	return $columns;
}
add_filter('manage_posts_columns', 'add_column', 5);

function column_content( $column, $id ){
	if( $column === 'post_id_clmn')
		echo $id;
}
add_action('manage_posts_custom_column', 'column_content', 5, 2);
get_the_id();
$postk = get_page_by_title( 'Your post title goes here', '', 'post' );
$postk->ID;
$postk = get_page_by_path('post-slug', '', 'post');
$postk->ID;
$postk = get_page_by_path('post-slug', '', 'post');
$postk->ID;

//list all posts of the user//
add_action( 'rest_api_init', 'custom_api_get_all_posts' );   

function custom_api_get_all_posts() {
    register_rest_route( 'custom/v1', '/all-posts', array(
        'methods' => 'GET',
        'callback' => 'custom_api_get_all_posts_callback'
    ));
}
function custom_api_get_all_posts_callback( $request ) {
    // Initialize the array that will receive the posts of Instagram data. 
    $posts_d = array();
    // Receive and set the page parameter from the $request for pagination purposes
    $paged = $request->get_param( 'page' );
    $paged = ( isset( $paged ) || ! ( empty( $paged ) ) ) ? $paged : 1; 
    // Get the posts using the 'post' and 'news' post types
    $posts = get_posts( array(
            'paged' => $paged,
            'post__not_in' => get_option( 'sticky_posts' ),
            'posts_per_page' => 10,            
            'post_type' => array( 'id', 'caption', 'image','posted stamp' ) // This is the line that allows to fetch all the post attributes
        )
    ); 
    // Loop through the posts and push the desired data to the array we've initialized earlier in the form of an object
    foreach( $posts as $post ) {
        $id = $post->ID; 
        $post_thumbnail = ( has_post_thumbnail( $id ) ) ? get_the_post_thumbnail_url( $id ) : null;

        $posts_d[] = (object) array( 
            'id' => $id, 
            'caption' => $post->post_caption, 
            'posted stamp' => $post->post stamp,
            'image' => $post->post_image,
            'featured_img_src' => $post_thumbnail
        );
    }                  
    return $posts_d;                   
}
//Adding pagination to the list at the end points//
public function listAction(Request $request)
{
        $upper_limit = $request->query->get('upper_limit', 10);
        $page = $request->query->get('page', 1);
}














