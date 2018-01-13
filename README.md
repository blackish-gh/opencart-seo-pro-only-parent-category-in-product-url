# seo-pro-only-parent-category-in-product-url

Works on Opencart 2.1.0.1 with Seo Pro<br>
When you need your seo links to have category hierarchy but need only parent category in product links<br>
This code only transforms product links<br>
Make sure you have turned on "category/subcategory/product" link structure in System - Settings - Server<br>
So as the result you will get "example.com/category/product" product links instead of "example.com/category/subcategory/sub-subcategory/product"

Installation.
1) Open /catalog/controller/common/seo_pro.php file<br>
Find 258 string :<br>
$path[$product_id] = $this->getPathByCategory($query->num_rows ? (int)$query->row['category_id'] : 0);<br>
Replace it with :<br>
$path[$product_id] = $this->getPathByCategoryP($query->num_rows ? (int)$query->row['category_id'] : 0);<br>
Place a new function 'getPathByCategoryP' after 'getPathByProduct' function :<br>
	private function getPathByCategoryP($category_id) {
		$category_id = (int)$category_id;
		if ($category_id < 1) return false;

		static $path = null;
		if (!isset($path)) {
			$path = $this->cache->get('categoryp.seopath');
			if (!isset($path)) $path = array();
		}

		if (!isset($path[$category_id])) {
			$max_level = 10;

			$sql = "SELECT CONCAT_WS('_'";
			for ($i = $max_level-1; $i >= 0; --$i) {
				$sql .= ",t$i.category_id";
			}
			$sql .= ") AS path FROM " . DB_PREFIX . "category t0";
			for ($i = 1; $i < $max_level; ++$i) {
				$sql .= " LEFT JOIN " . DB_PREFIX . "category t$i ON (t$i.category_id = t" . ($i-1) . ".parent_id)";
			}
			$sql .= " WHERE t0.category_id = '" . $category_id . "'";

			$query = $this->db->query($sql);

			$parent_cat = stristr($query->row['path'], '_', true);
			if($parent_cat != false) {
			$path[$category_id] = $query->num_rows ? $parent_cat : false;
			} else {
				$path[$category_id] = $category_id;
			}
			
			$this->cache->set('categoryp.seopath', $path);
		}

		return $path[$category_id];
	}
  
  Or just simply replace your own file with the one from repository<br>
  2) Then Refresh the modificators in admin panel and delete all files except 'index.html' in system/cache or system/storage/cache
  
  Работает на Opencart 2.1.0.1 с установленным Seo Pro<br>
  Данный код оставляет в ссылках на товар только родительскую категорию, не трогая при этом ссылки на категории<br>
  Переключатель "ЧПУ товаров с категориями" должен быть включен в Система - Настройки - Сервер<br>
  В результате получим такие ссылки на товар "example.com/category/product" вместо таких "example.com/category/subcategory/sub-subcategory/product"
  
  Установка.
  1) Откройте /catalog/controller/common/seo_pro.php файл<br>
Ищите 258 строку :<br>
$path[$product_id] = $this->getPathByCategory($query->num_rows ? (int)$query->row['category_id'] : 0);<br>
Замените ее этой :<br>
$path[$product_id] = $this->getPathByCategoryP($query->num_rows ? (int)$query->row['category_id'] : 0);<br>
Разместите новую функцию 'getPathByCategoryP' после функции 'getPathByProduct' :<br>
	private function getPathByCategoryP($category_id) {
		$category_id = (int)$category_id;
		if ($category_id < 1) return false;

		static $path = null;
		if (!isset($path)) {
			$path = $this->cache->get('categoryp.seopath');
			if (!isset($path)) $path = array();
		}

		if (!isset($path[$category_id])) {
			$max_level = 10;

			$sql = "SELECT CONCAT_WS('_'";
			for ($i = $max_level-1; $i >= 0; --$i) {
				$sql .= ",t$i.category_id";
			}
			$sql .= ") AS path FROM " . DB_PREFIX . "category t0";
			for ($i = 1; $i < $max_level; ++$i) {
				$sql .= " LEFT JOIN " . DB_PREFIX . "category t$i ON (t$i.category_id = t" . ($i-1) . ".parent_id)";
			}
			$sql .= " WHERE t0.category_id = '" . $category_id . "'";

			$query = $this->db->query($sql);

			$parent_cat = stristr($query->row['path'], '_', true);
			if($parent_cat != false) {
			$path[$category_id] = $query->num_rows ? $parent_cat : false;
			} else {
				$path[$category_id] = $category_id;
			}
			
			$this->cache->set('categoryp.seopath', $path);
		}

		return $path[$category_id];
	}
  
  Или просто замените файл целиком<br>
  2) Потом обновите модификаторы в админке и удалите все файлы за исключением 'index.html' в system/cache или system/storage/cache 
  
