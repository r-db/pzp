# WordPress PZP Implementation

> Add PZP to WordPress in 20 minutes. Works with any theme.

## Overview

WordPress powers 43% of the web, making it the most important platform for PZP adoption. This guide covers:
- Self-hosted WordPress (full control)
- WordPress.com (limited, subdomain approach)
- WooCommerce (e-commerce)

---

## Quick Start (Self-Hosted)

### Option A: Static Files in Theme

The simplest approach for basic PZP:

**1. Create PZP directory:**
```bash
mkdir -p wp-content/themes/your-theme/pzp/{context,actions,data}
```

**2. Add core files:**

**wp-content/themes/your-theme/pzp/manifest.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "protocolVersion": "1.0.0",
  "name": "Your WordPress Site",
  "description": "Your site description",
  "type": "content",
  "updated": "2026-03-17T00:00:00Z",
  "tokenBudgets": {
    "essential": 400,
    "full": 2000
  },
  "endpoints": {
    "context": "/pzp/context/",
    "actions": "/pzp/actions/",
    "data": "/pzp/data/"
  },
  "capabilities": ["read", "search"]
}
```

**3. Add routing in functions.php:**
```php
<?php
// Serve PZP files with correct headers
add_action('init', function() {
    // Match /pzp/* requests
    if (preg_match('#^/pzp/(.+)$#', $_SERVER['REQUEST_URI'], $matches)) {
        $file = $matches[1];
        $pzp_dir = get_template_directory() . '/pzp/';
        $file_path = $pzp_dir . $file;

        if (file_exists($file_path)) {
            // Set PZP headers
            header('X-PZP-Site: true');
            header('X-PZP-Version: 1.0.0');
            header('Access-Control-Allow-Origin: *');

            // Set content type
            if (str_ends_with($file, '.md')) {
                header('Content-Type: text/markdown; charset=utf-8');
            } elseif (str_ends_with($file, '.json')) {
                header('Content-Type: application/json; charset=utf-8');
            }

            readfile($file_path);
            exit;
        }
    }
});
```

**4. Test:**
```bash
curl https://yoursite.com/pzp/manifest.json
curl -I https://yoursite.com/pzp/manifest.json | grep -i pzp
```

---

### Option B: PZP Plugin (Recommended)

Create a dedicated plugin for more control:

**wp-content/plugins/pzp/pzp.php:**
```php
<?php
/**
 * Plugin Name: PZP - Prime Zero Protocol
 * Description: Machine-native interface for your WordPress site
 * Version: 1.0.0
 */

defined('ABSPATH') || exit;

class WP_PZP {
    private $version = '1.0.0';

    public function __construct() {
        add_action('init', [$this, 'register_routes']);
        add_action('rest_api_init', [$this, 'register_api_routes']);
        add_filter('robots_txt', [$this, 'modify_robots_txt'], 10, 2);
    }

    /**
     * Register PZP file routes
     */
    public function register_routes() {
        add_rewrite_rule(
            '^pzp/(.+)$',
            'index.php?pzp_file=$matches[1]',
            'top'
        );
        add_rewrite_tag('%pzp_file%', '([^&]+)');
    }

    /**
     * Handle PZP file requests
     */
    public function handle_request() {
        $file = get_query_var('pzp_file');
        if (!$file) return;

        $this->set_pzp_headers();

        // Route to appropriate handler
        switch ($file) {
            case 'manifest.json':
                $this->serve_manifest();
                break;
            case 'index.md':
                $this->serve_index();
                break;
            case 'context/essential.md':
                $this->serve_essential_context();
                break;
            case 'context/full.md':
                $this->serve_full_context();
                break;
            case 'actions/index.json':
                $this->serve_actions();
                break;
            case 'data/posts.json':
                $this->serve_posts_index();
                break;
            default:
                // Try to serve static file
                $this->serve_static_file($file);
        }
        exit;
    }

    /**
     * Set PZP headers
     */
    private function set_pzp_headers() {
        header('X-PZP-Site: true');
        header('X-PZP-Version: ' . $this->version);
        header('Access-Control-Allow-Origin: *');
        header('Cache-Control: public, max-age=300');
    }

    /**
     * Generate manifest dynamically
     */
    private function serve_manifest() {
        header('Content-Type: application/json; charset=utf-8');

        $manifest = [
            '$schema' => 'https://pzp.riscent.com/schemas/manifest.schema.json',
            'version' => $this->version,
            'protocolVersion' => '1.0.0',
            'name' => get_bloginfo('name'),
            'description' => get_bloginfo('description'),
            'type' => 'content',
            'updated' => get_lastpostmodified('gmt') . 'Z',
            'tokenBudgets' => [
                'essential' => 400,
                'full' => 2000
            ],
            'endpoints' => [
                'context' => '/pzp/context/',
                'actions' => '/pzp/actions/',
                'data' => '/pzp/data/'
            ],
            'capabilities' => ['read', 'search']
        ];

        echo json_encode($manifest, JSON_PRETTY_PRINT);
    }

    /**
     * Generate essential context from site settings
     */
    private function serve_essential_context() {
        header('Content-Type: text/markdown; charset=utf-8');

        $name = get_bloginfo('name');
        $desc = get_bloginfo('description');
        $categories = get_categories(['hide_empty' => true]);
        $recent = wp_get_recent_posts(['numberposts' => 5]);

        $content = "# {$name} Essential Context\n\n";
        $content .= "## What This Is\n\n{$desc}\n\n";

        $content .= "## Categories\n\n";
        $content .= "| Category | Posts |\n|----------|-------|\n";
        foreach ($categories as $cat) {
            $content .= "| {$cat->name} | {$cat->count} |\n";
        }

        $content .= "\n## Recent Posts\n\n";
        foreach ($recent as $post) {
            $content .= "- [{$post['post_title']}](/pzp/data/posts/{$post['ID']}.md)\n";
        }

        $content .= "\n## Primary Actions\n\n";
        $content .= "| Action | Description |\n";
        $content .= "|--------|-------------|\n";
        $content .= "| search-posts | Search all posts |\n";
        $content .= "| list-posts | List posts by category |\n";
        $content .= "| get-post | Get specific post |\n";

        echo $content;
    }

    /**
     * Generate posts index
     */
    private function serve_posts_index() {
        header('Content-Type: application/json; charset=utf-8');

        $posts = get_posts([
            'numberposts' => 100,
            'post_status' => 'publish'
        ]);

        $data = [
            'posts' => array_map(function($post) {
                return [
                    'id' => $post->ID,
                    'title' => $post->post_title,
                    'slug' => $post->post_name,
                    'excerpt' => wp_trim_words($post->post_content, 30),
                    'date' => $post->post_date_gmt . 'Z',
                    'modified' => $post->post_modified_gmt . 'Z',
                    'categories' => wp_get_post_categories($post->ID, ['fields' => 'names']),
                    'path' => "/pzp/data/posts/{$post->ID}.md",
                    'tokens' => $this->estimate_tokens($post->post_content)
                ];
            }, $posts),
            'total' => count($posts),
            'generated' => current_time('c')
        ];

        echo json_encode($data, JSON_PRETTY_PRINT);
    }

    /**
     * Serve actions definition
     */
    private function serve_actions() {
        header('Content-Type: application/json; charset=utf-8');

        $actions = [
            '$schema' => 'https://pzp.riscent.com/schemas/actions.schema.json',
            'version' => '1.0.0',
            'baseUrl' => home_url('/pzp'),
            'actions' => [
                [
                    'name' => 'search-posts',
                    'description' => 'Search all posts',
                    'endpoint' => '/wp-json/pzp/v1/search',
                    'method' => 'GET',
                    'input' => [
                        'query' => ['type' => 'string', 'required' => true],
                        'category' => ['type' => 'string'],
                        'limit' => ['type' => 'integer', 'default' => 10]
                    ]
                ],
                [
                    'name' => 'list-posts',
                    'description' => 'List posts with filters',
                    'endpoint' => '/pzp/data/posts.json',
                    'method' => 'GET',
                    'input' => []
                ],
                [
                    'name' => 'get-post',
                    'description' => 'Get a specific post',
                    'endpoint' => '/pzp/data/posts/{id}.md',
                    'method' => 'GET',
                    'input' => [
                        'id' => ['type' => 'integer', 'required' => true]
                    ]
                ]
            ]
        ];

        echo json_encode($actions, JSON_PRETTY_PRINT);
    }

    /**
     * Register REST API search endpoint
     */
    public function register_api_routes() {
        register_rest_route('pzp/v1', '/search', [
            'methods' => 'GET',
            'callback' => [$this, 'api_search'],
            'permission_callback' => '__return_true'
        ]);
    }

    /**
     * Handle search API
     */
    public function api_search($request) {
        $query = $request->get_param('query');
        $category = $request->get_param('category');
        $limit = $request->get_param('limit') ?: 10;

        $args = [
            's' => $query,
            'posts_per_page' => $limit,
            'post_status' => 'publish'
        ];

        if ($category) {
            $args['category_name'] = $category;
        }

        $search = new WP_Query($args);

        $results = array_map(function($post) {
            return [
                'id' => $post->ID,
                'title' => $post->post_title,
                'excerpt' => wp_trim_words($post->post_content, 30),
                'path' => "/pzp/data/posts/{$post->ID}.md",
                'relevance' => 1.0
            ];
        }, $search->posts);

        return [
            'results' => $results,
            'total' => $search->found_posts,
            'query' => $query
        ];
    }

    /**
     * Add PZP headers to robots.txt
     */
    public function modify_robots_txt($output, $public) {
        $output .= "\n# PZP Discovery\n";
        $output .= "X-PZP-Site: true\n";
        $output .= "X-PZP-Manifest: /pzp/manifest.json\n";
        $output .= "X-PZP-Version: {$this->version}\n";
        return $output;
    }

    /**
     * Estimate token count
     */
    private function estimate_tokens($content) {
        return (int)(str_word_count(strip_tags($content)) * 1.3);
    }
}

// Initialize
add_action('plugins_loaded', function() {
    new WP_PZP();
});

// Flush rewrite rules on activation
register_activation_hook(__FILE__, function() {
    $pzp = new WP_PZP();
    $pzp->register_routes();
    flush_rewrite_rules();
});
```

**Activate the plugin:**
```bash
wp plugin activate pzp
```

---

## WooCommerce Integration

For WooCommerce stores, extend the plugin:

```php
<?php
// Add to pzp.php or create separate file

class WP_PZP_WooCommerce {
    public function __construct() {
        add_action('rest_api_init', [$this, 'register_routes']);
    }

    public function register_routes() {
        register_rest_route('pzp/v1', '/products', [
            'methods' => 'GET',
            'callback' => [$this, 'get_products'],
            'permission_callback' => '__return_true'
        ]);

        register_rest_route('pzp/v1', '/products/search', [
            'methods' => 'GET',
            'callback' => [$this, 'search_products'],
            'permission_callback' => '__return_true'
        ]);
    }

    public function get_products($request) {
        $args = [
            'status' => 'publish',
            'limit' => $request->get_param('limit') ?: 20,
            'offset' => $request->get_param('offset') ?: 0,
            'category' => $request->get_param('category') ?: [],
            'orderby' => 'date',
            'order' => 'DESC'
        ];

        $products = wc_get_products($args);

        return [
            'products' => array_map([$this, 'format_product'], $products),
            'total' => count($products),
            'generated' => current_time('c')
        ];
    }

    public function search_products($request) {
        $query = $request->get_param('query');
        $category = $request->get_param('category');
        $min_price = $request->get_param('minPrice');
        $max_price = $request->get_param('maxPrice');
        $in_stock = $request->get_param('inStock');

        $args = [
            'status' => 'publish',
            'limit' => $request->get_param('limit') ?: 20,
            's' => $query
        ];

        if ($category) {
            $args['category'] = [$category];
        }

        if ($min_price) {
            $args['min_price'] = $min_price;
        }

        if ($max_price) {
            $args['max_price'] = $max_price;
        }

        if ($in_stock === 'true') {
            $args['stock_status'] = 'instock';
        }

        $products = wc_get_products($args);

        return [
            'results' => array_map([$this, 'format_product'], $products),
            'total' => count($products),
            'query' => $query
        ];
    }

    private function format_product($product) {
        return [
            'id' => $product->get_id(),
            'name' => $product->get_name(),
            'slug' => $product->get_slug(),
            'description' => wp_trim_words($product->get_description(), 50),
            'shortDescription' => $product->get_short_description(),
            'price' => [
                'amount' => (float) $product->get_price(),
                'regular' => (float) $product->get_regular_price(),
                'sale' => $product->get_sale_price() ? (float) $product->get_sale_price() : null,
                'currency' => get_woocommerce_currency()
            ],
            'inStock' => $product->is_in_stock(),
            'stockQuantity' => $product->get_stock_quantity(),
            'categories' => wp_get_post_terms($product->get_id(), 'product_cat', ['fields' => 'names']),
            'images' => array_map(function($id) {
                return wp_get_attachment_url($id);
            }, $product->get_gallery_image_ids()),
            'url' => $product->get_permalink()
        ];
    }
}

// Initialize WooCommerce extension if WooCommerce is active
add_action('plugins_loaded', function() {
    if (class_exists('WooCommerce')) {
        new WP_PZP_WooCommerce();
    }
});
```

**WooCommerce Actions:**

```json
{
  "actions": [
    {
      "name": "search-products",
      "description": "Search products in the store",
      "endpoint": "/wp-json/pzp/v1/products/search",
      "method": "GET",
      "input": {
        "query": { "type": "string", "required": true },
        "category": { "type": "string" },
        "minPrice": { "type": "number" },
        "maxPrice": { "type": "number" },
        "inStock": { "type": "boolean" },
        "limit": { "type": "integer", "default": 20 }
      }
    },
    {
      "name": "list-products",
      "description": "List all products",
      "endpoint": "/wp-json/pzp/v1/products",
      "method": "GET",
      "input": {
        "category": { "type": "string" },
        "limit": { "type": "integer", "default": 20 },
        "offset": { "type": "integer", "default": 0 }
      }
    }
  ]
}
```

---

## Subdomain Approach (WordPress.com or Any)

For WordPress.com or when you want separation:

### 1. Set Up Separate PZP Host

Use Vercel, Netlify, or Cloudflare Pages:

```bash
# Create PZP site
mkdir pzp-wordpress && cd pzp-wordpress
npm init -y
```

### 2. Sync Script

**sync-pzp.js:**
```javascript
const fetch = require('node-fetch');
const fs = require('fs');

const WP_URL = 'https://yoursite.wordpress.com';
const WP_API = `${WP_URL}/wp-json/wp/v2`;

async function syncPZP() {
  // Fetch posts
  const postsRes = await fetch(`${WP_API}/posts?per_page=100`);
  const posts = await postsRes.json();

  // Generate posts index
  const postsIndex = {
    posts: posts.map(post => ({
      id: post.id,
      title: post.title.rendered,
      slug: post.slug,
      excerpt: post.excerpt.rendered.replace(/<[^>]*>/g, '').trim(),
      date: post.date_gmt + 'Z',
      categories: post.categories,
      path: `/data/posts/${post.id}.md`
    })),
    total: posts.length,
    generated: new Date().toISOString()
  };

  fs.writeFileSync('public/data/posts.json', JSON.stringify(postsIndex, null, 2));

  // Generate individual post files
  for (const post of posts) {
    const markdown = `# ${post.title.rendered}\n\n${post.content.rendered}`;
    fs.writeFileSync(`public/data/posts/${post.id}.md`, markdown);
  }

  // Update manifest
  const manifest = JSON.parse(fs.readFileSync('public/manifest.json', 'utf8'));
  manifest.updated = new Date().toISOString();
  fs.writeFileSync('public/manifest.json', JSON.stringify(manifest, null, 2));

  console.log(`Synced ${posts.length} posts`);
}

syncPZP();
```

### 3. Deploy to Vercel

```bash
vercel deploy --prod
```

### 4. Configure DNS

Add CNAME record:
```
pzp.yoursite.com → cname.vercel-dns.com
```

---

## Caching & Performance

### Add Caching Headers

```php
// In plugin or functions.php
add_action('send_headers', function() {
    if (strpos($_SERVER['REQUEST_URI'], '/pzp/') === 0) {
        header('Cache-Control: public, max-age=300');
        header('Vary: Accept-Encoding');
    }
});
```

### Use Object Cache

```php
private function serve_posts_index() {
    $cache_key = 'pzp_posts_index';
    $data = wp_cache_get($cache_key);

    if (!$data) {
        $data = $this->generate_posts_index();
        wp_cache_set($cache_key, $data, '', 300);
    }

    header('Content-Type: application/json; charset=utf-8');
    echo json_encode($data, JSON_PRETTY_PRINT);
}
```

### Invalidate on Publish

```php
add_action('publish_post', function() {
    wp_cache_delete('pzp_posts_index');
    wp_cache_delete('pzp_essential_context');
});
```

---

## Testing

```bash
# Test manifest
curl https://yoursite.com/pzp/manifest.json | jq .

# Test headers
curl -I https://yoursite.com/pzp/manifest.json | grep -i pzp

# Test context
curl https://yoursite.com/pzp/context/essential.md

# Test search
curl "https://yoursite.com/wp-json/pzp/v1/search?query=wordpress" | jq .

# Test products (WooCommerce)
curl "https://yoursite.com/wp-json/pzp/v1/products/search?query=shirt" | jq .
```

---

## Troubleshooting

### 404 on PZP routes

Flush rewrite rules:
```bash
wp rewrite flush
```

Or visit Settings → Permalinks and click Save.

### Headers not appearing

Check if another plugin is overriding. Add headers earlier:
```php
add_action('init', function() {
    // ... header logic
}, 1);
```

### REST API errors

Ensure REST API is enabled and accessible:
```bash
curl https://yoursite.com/wp-json/
```

---

*WordPress: PZP for 43% of the web.*
