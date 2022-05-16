# ![alt text](./img/logoLaravel.png) I - Responsabilités unique (Single Responsibility Principle)
 
##### **Lien du tutoriel © Nord Coders  : https://www.youtube.com/watch?v=XRdmfIpeqSk&list=PLeeuvNW2FHVjp0PtY8PF5S6okZcDIA4UJ&index=1&ab_channel=NordCoders**


Durant ce chapitre, nous allons aborder le principe de responsabilité unique.

Le principie de responsabilité unique affirme qu'une classe ou une méthode ne doit avoir qu'une seule de changer. 
C'est assez abstrait je vous le confirme !

C'est pourquoi, nous allons voir un exemple de ce principe.

Pour cela, nous créeons un projet laravel, nous mettons en place un système d'authentification. 

Et nous créeons un Model `` Order.php`` et également un <a href="../LaravelFeatures/1.5-Factories.md">Factory</a> `` OrderFactory.php``

*<u>Code du fichier ``Order.php`` : </u>*

    <?php
    namespace App\Models;
    use Illuminate\Database\Eloquent\Model;
    class Order extends Model
    {
        use HasFactory
    }

*<u>Code du fichier ``OrderFactory.php`` : </u>*

    <?php
    namespace App\Factories;
    use App\Models\Order;
    class OrderFactory extends Factory
    {
        public function definition(){
            return[
                'shipped_at'=>$this->faker->dateTimeBetween('-2 years'), 
                'total_price'=>$this->faker->numberBetween(1000,50000),
            ]
        }
    }

Puis on génère environ une centaine.

Le but ici est de générer des rapports de commandes qui puisse être formater d'une certaine manière, ce rapport nous renvoie toutes les commandes passées entre deux dates.

Nous allons donc créer un dossier ``App/Services/Order`` dans lequel nous allons créer un <a href="../LaravelFeatures/3.6-ProvidersServices.md">Service</a>  ``OrderReport.php``.

*<u>Code du fichier ``OrderReport.php`` : </u>*

    <?php
    namespace App\Services\Order;

    use Carbon\CarbonInterface;

    class OrderReport
    {
        public function orderBetween(CarbonInterface $startDate, CarbonInterface $endDate){
            return Order::whereBetween('shipped_at', [$startDate, $endDate])->latest()->get();
        }
    }

Pour tester si notre service fonctionne, nous nous rendons dans notre fichier de routes ``routes/web.php``, et nous mettons en place une route qui nous renvoie un rapport de commandes.

    Route::get('/', function(){
        return (new OrderReport())->orderBetween(now()->subMonths(6), now());
    }); 


Nous allons désormais faire retourner une chaine de caractère simulant un rapport de commandes dans le fichier ``OrderReport.php`` .

*<u>Code du fichier ``OrderReport.php`` : </u>*

    <?php
    namespace App\Services\Order;

    use Carbon\CarbonInterface;

    class OrderReport
    {
        public function orderBetween(CarbonInterface $startDate, CarbonInterface $endDate){
            $order =  Order::whereBetween('shipped_at', [$startDate, $endDate])->latest()->get();

            return $this->PDFFormat();  
        }
    }

    public function PDFFormat(){
        return "PDF formatted"; 
    }

Cependant faire ce genre de code, n'est pas très conseillé. En effet, il préférable de laisser la classe `` OrderReport`` comme elle était lors de sa 1ère version . Et de déplacer la méthode ``PDFFormat()`` dans un autre fichier.

Nous allons mettre notre méthode ``PDFFormat()`` dans un fichier que l'on va nommé ``App/Services/Format/PDFFormat.php`` : 

    <?php
    namespace App\Services\Format;

    class PDFFormat
    {
        public function format(Collection $data)
        {
            return "PDF formatted";
        }
    }

Nous nous rendons dans notre fichier ``web.php`` et nous rajoutons cela dans notre route : 

    return (new PDFFormat())->format($orders);

Et si nous souhaitons changer le format de notre rapport, nous allons créer un nouveau fichier ``App/Services/Format/HTMLFormat.php`` : 

    <?php
    namespace App\Services\Format;

    class HTMLFormat
    {
        public function format(Collection $data)
        {
            return "HTML formatted";
        }
    }

Ainsi, avec cette façon de faire chaque classe, ces dernières n'ont qu'une seule raison de changer.