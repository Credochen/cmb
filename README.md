<h1 align="center"> cmb </h1>

<p align="center"> 招商银行聚合支付 SDK </p>

## Installing

```shell
$ composer require chenpkg/cmb -vvv
```

## Usage

```php
require './vendor/autoload.php';

use Cmb\Factory;
use Cmb\Payment\PolyPay\Client;

$config = [
    'appid'  => 'appid',
    'secret' => 'secret',
    // 商户 ID
    'mer_id' => 'mer_id',
    // 收银员 ID
    'user_id' => 'user_id',
    // 商户公钥
    'public_key' => '...',
    // 商户私钥
    'private_key' => '...',
    // 招行公钥
    'cmb_public_key' => '...',

    // 是否开启测试环境
    'test' => true
];

// 扩展 api 接口, 添加宏方法
// 例如：添加银联云闪付api
Client::macro('cloudPay', function ($params) {
    // 如果需要同步验签
    return $this->requestVerify('mchorders/cloudpay', $params);
    // 不需要同步验签
    return $this->request('mchorders/cloudpay', $params);
});

$app = Factory::payment($config);

$orderId = date('YmdHis').random_int(1000, 9999);

$params = [
    'orderId'    => $orderId,
    'notifyUrl'  => 'https://localhost/payment/notify',
    'txnAmt'     => 0.1 * 100,
    'tradeScene' => 'OFFLINE',
];

// 调用自定义方法宏 api
$app->polypay->cloudPay($params);

// 二维码支付
$app->polypay->qrCode($params);

// 查询订单
$app->polypay->orderQuery($orderId);

// 订单退款
$app->polypay->refund($refundData);

// 查询退款订单
$app->polypay->refundQuery($refundOrderId);

// 关闭订单
$app->polypay->close($orderId);

// 订单二维码
$app->polypay->orderQrCode($params);

// 微信统一下单
$app->polypay->onlinePay($params);

// 支付宝服务窗支付
$app->polypay->aliServerPay($params);

// 支付宝二维码
$app->polypay->aliQrCode($params);

// 微信小程序支付
$app->polypay->miniAppOrder($params);

// 微信二维码支付
$app->polypay->wxQrCode($params);
```

## 消息通知

```php
$response = $app->handlePaidNotify(function ($message, $fail) {
    // data
    var_dump($message);

    // 你的逻辑

    return true;

    // 或者错误消息
    return $fail('Order not exists.');
});

$response->send(); // Laravel 里请使用：return $response;
```

## License

MIT
