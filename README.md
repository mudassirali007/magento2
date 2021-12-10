1. RUN THIS COMMAND(Steps: https://javapocalypse.medium.com/install-magento-2-4-on-windows-10-2021-a0c433e4ffc4  OR https://www.thecoachsmb.com/6-steps-to-install-magento2-4-2-on-xampp-windows-using-composer/)

 DOWNLOAD ZIP 2.4.1 (https://magento.com/tech-resources/download?_ga=2.60843244.1363205642.1627541184-263031044.1627290278)

2. HERE you will find access Keys create a new one that is your username and password

 https://marketplace.magento.com/customer/accessKeys/

3. install elastic search 
  https://www.elastic.co/guide/en/elasticsearch/reference/current/zip-windows.html

3.1. RUN Elastic

C:\elasticsearch-7.13.4>.\bin\elasticsearch.bat -Ecluster.name=my_cluster -Enode.name=node_1

3.2 Steps in Code  

//vendor\magento\framework\Image\Adapter\G2d.php on LINE 86 Replace Function 
	private function validateURLScheme(string $filename) : bool
{
   $allowed_schemes = ['ftp', 'ftps', 'http', 'https'];
   $url = parse_url($filename);
   if ($url && isset($url['scheme']) && !in_array($url['scheme'], $allowed_schemes) && !file_exists($filename)) 
    {
       return false;
     }
   return true;
}
//vendor\magento\framework\View\Element\Template\File\Validator.php on LINE 138 Replace Function 
$realPath = str_replace('\\', '/',$this->fileDriver->getRealPath($path));
//vendor\magento\framework\Interception\PluginListGenerator.php on LINE 158 Replace Function 
$cacheId = implode('-', $this->scopePriorityScheme) . '-' . $this->cacheId;

3.3 Enable these in php.ini
 sockets,soap,intl,sodium

4. run the following command accordingly

php -d memory_limit=-1 bin/magento setup:install --base-url="http://localhost/magento2" --db-host="localhost" --db-name="magento2" --db-user="root" --db-password="" --admin-firstname="admin" --admin-lastname="admin" --admin-email="user@example.com" --admin-user="admin" --admin-password="admin123" --use-rewrites="1" --backend-frontname="admin" --search-engine=elasticsearch7 --elasticsearch-host="localhost" --elasticsearch-port=9200

php -d memory_limit=-1 bin/magento setup:di:compile

php -d memory_limit=-1 bin/magento indexer:reindex

php -d memory_limit=-1 bin/magento setup:upgrade

php -d memory_limit=-1 bin/magento setup:static-content:deploy –f (try this multiple times because it will give error)

php -d memory_limit=-1 bin/magento deploy:mode:set developer

php -d memory_limit=-1 bin/magento cache:clean

php -d memory_limit=-1 bin/magento cache:flush

php -d memory_limit=-1 bin/magento module:disable Magento_Csp

php -d memory_limit=-1 bin/magento module:disable Magento_TwoFactorAuth

IN DB: INSERT INTO `core_config_data`(`path`, `value`) VALUES ('dev/static/sign', 0) ON DUPLICATE KEY UPDATE `value`=0

5. in httpd.conf of apache uncomment this module
LoadModule version_module modules/mod_version.so

6. if loader having error then goto app/etc/di.xml AND 
REPLACE
Magento\Framework\App\View\Asset\MaterializationStrategy\Symlink
WITH
Magento\Framework\App\View\Asset\MaterializationStrategy\Copy


After override:
php -d memory_limit=-1 bin/magento setup:upgrade

php -d memory_limit=-1 bin/magento setup:di:compile

php -d memory_limit=-1 bin/magento setup:static-content:deploy

php -d memory_limit=-1 bin/magento cache:clean

php -d memory_limit=-1 bin/magento cache:flush
