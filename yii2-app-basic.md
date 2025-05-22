# Yii2 basic app guidelines

## Overview

This document outlines the coding standards and structural conventions that **Junie**, the AI agent, must follow when working with PHP projects based on the **Yii2 framework**, specifically the **basic application template** (`yii2-app-basic`). These guidelines are designed to ensure code consistency, maintainability, and best practices aligned with the Yii2 ecosystem and PSR standards.

## Directory Structure

Junie must respect the standard Yii2 basic application directory layout:

* `assets/`: Asset bundles for frontend resources (CSS/JS).
* `commands/`: Console command controllers extending `yii\console\Controller`. 
* `components/`: Custom application components or services.
* `config/`: Application configuration files (`web.php`, `db.php`, `params.php`, etc.).
* `controllers/`: Web controllers (MVC C layer), each extending `yii\web\Controller`.
* `mail/`: Email view templates (HTML/text).
* `models/`: Data models (AR and form models).
* `modules/`: Optional sub-applications, each self-contained.
* `runtime/`: Temporary runtime data (logs, cache, sessions).
* `tests/`: Unit and functional tests.
* `vendor/`: Composer dependencies (must not be modified directly).
* `views/`: View files (MVC V layer) organized per controller.
* `widgets/`: Reusable frontend widgets.
* `web/`: Public root (contains `index.php`, public assets).

## Controllers

* Must extend `yii\web\Controller`.
* Must follow the naming pattern `XyzController` (e.g., `SiteController`).
* Actions are public methods prefixed with `action` (e.g., `actionIndex`).
* Views for each action are stored in `views/<controller-id>/<action-id>.php`.

### Best Practices

* Keep logic minimal in controllers. Use models or services for business logic.
* Sanitize input and pass only validated data to views.
* Do not manipulate the database or session directly in views.

## Models

### Active Record (AR) Models

* Extend `yii\db\ActiveRecord`.
* Represent database tables.
* Define `rules()`, `relations`, `scenarios`, and custom business methods.

### Form Models

* Extend `yii\base\Model`.
* Used for validation and temporary data structures (e.g., login forms).

### Best Practices

* Validation logic must live in `rules()`.
* Business logic must reside in models or dedicated service classes.
* Avoid tight coupling with `Yii::$app`; models should be testable in isolation.

## Views

* Stored in `views/<controller-id>/`.
* Contain primarily HTML with minimal PHP.
* Must not contain database access or complex business logic.
* Must escape dynamic content using `Html::encode()` unless safe.

### Layouts and Partials

* Layouts live in `views/layouts/`.
* Use partials to organize complex views.

## Components

* Located in `components/`.
* Used for shared services or logic.
* Extend `yii\base\Component` if needing configuration, events, or DI.
* Register in `config/web.php` under `components` to be accessible via `Yii::$app`.

```php
// Example: Registering a custom component
'components' => [
    'alertService' => [
        'class' => 'app\components\AlertService',
        'defaultLevel' => 'info',
    ],
],
```

## Widgets

* Reside in `widgets/`.
* Extend `yii\base\Widget`.
* Encapsulate reusable UI blocks.
* Must include configurable properties and optionally render views.

```php
namespace app\widgets;

use yii\base\Widget;

class HelloWidget extends Widget {
    public $name;
    public function run() {
        return "<p>Hello {$this->name}</p>";
    }
}
```

Usage: `<?= HelloWidget::widget(['name' => 'Junie']) ?>`

## Modules

* Located in `modules/<module-name>/`.
* Contain their own `Module.php`, `controllers/`, `models/`, `views/`, etc.
* Main module class must extend `yii\base\Module`.
* Must be registered in `config/web.php` under `modules`.

```php
'modules' => [
    'admin' => [
        'class' => 'app\modules\admin\Module',
    ],
],
```

## PSR Standards

* **PSR-1 & PSR-12**: Code style (4 spaces indentation, proper visibility, etc.).
* **PSR-4**: Namespace-based autoloading.
* Use `StudlyCaps` for class names, `camelCase` for methods/properties.
* One class per file. UTF-8 encoding. No closing `?>`.

## Application Configuration

* Store sensitive data in environment variables or `params.php`, not in code.
* Use `YII_DEBUG` and `YII_ENV` flags to control environment-specific behavior.
* Keep logic out of entry scripts (`web/index.php`).
* Use `params` for global constants/settings: `Yii::$app->params['key']`.

## Business Logic Structure

* Follow MVC strictly.
* Fat Models, Thin Controllers.
* Use service classes for workflows involving multiple models.
* Use events or behaviors for decoupled logic.

## Documentation Standards

* Use phpDoc for all classes and public/protected methods.
* Document properties with `@property` for AR models.
* Prefer concise, clear summaries and consistent formatting.

```php
/**
 * Sends a welcome email.
 * @param User $user
 * @return bool
 */
public function sendWelcomeEmail(User $user): bool
```
