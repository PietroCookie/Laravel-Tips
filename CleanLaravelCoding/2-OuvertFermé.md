# ![alt text](./img/logoLaravel.png) I - Ouvert/Fermé (Open/Closed Principle)
 
#### **Lien du tutoriel © Nord Coders  : https://www.youtube.com/watch?v=zMdwBk-kQGw&list=PLeeuvNW2FHVjp0PtY8PF5S6okZcDIA4UJ&index=2&ab_channel=NordCoders

Durant ce chapitre, nous allons aborder le principe d'ouvert et fermé.

Le principe ouvert/fermé affirme qu'une classe doit être à la fois ouverte (à l'extension) et fermée (à la modification). 

Afin d'exploiter ce principe, nous allons dans un premier temps créer un <a href="../LaravelFeatures/3.6-ProvidersServices.md">Services</a> permettant de payer une commande. 

Path du fichier : ``App/Services/Payment/PaymentService.php``
    
*<u>Code du fichier ``PaymentService.php`` : </u>*

    <?php
    namespace App\Services\Payment;
    class PaymentService{
        public function processPayment($paymentMethod){
            if($paymentMethod == 'paypal'){
                return 'Payment processed with Paypal';
            }elseif($paymentMethod == 'stripe'){
                return 'Payment processed with Stripe';
            }else{
                return 'Payment method not supported';
            }
        }
    }

Afin d'utiliser ce service nous allons créer une route dans le fichier ``routes/web.php`` (généralement dans un vrai application on ne fait pas cela, nous souhaitons juste tester le principe) : 

    Route::get('/pay', function(){
        return (new PaymentService())->processPayment('paypal');
    }); 

On se rend maintenant, sur l'url ``/pay ``, on a bien le phrase correspondant.

Cependant, avec ce genre de code, on ne respecte pas le principe ouvert/fermé.

Pour respecter ce principe, à chaque fois que l'on a un ``if``on devrait avoir une classe qui va appelé la ``paymentMethod`` en particulier. 

`` Time : 5'03 ``

