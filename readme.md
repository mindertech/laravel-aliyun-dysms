## Installation

1. add the following to your composer.json. Then run `composer update`:

    ```json
    "pgroot/laravel-aliyun-dysms": "dev-master"
    ```

    or

    ```bash
    composer require pgroot/laravel-aliyun-dysms
    ```

2. Run the command below to publish the package config file `config/dysms.php`:

    ```shell
    php artisan vendor:publish --provider=Mindertech\Dysms\MindertechDysmsServiceProvider
    ```

## Configuration

Set the property values in the `config/dysms.php`.

default
```php
return [
    'access_key_id' => '',
    'access_key_secret' => '',
    'sign' => '',
    'log' => false,
    'sms-report-queue' => '',
    'sms-up-queue' => '',

    //以下配置暂时无需替换
    'product' => 'Dysmsapi',
    'domain' => 'dysmsapi.aliyuncs.com',
    'region' => 'cn-hangzhou',
    'end_point_name' => 'cn-hangzhou',
    'mns' => [
        'account_id' => '1943695596114318',
        'product' => 'Dybaseapi',
        'domain' => 'dybaseapi.aliyuncs.com',
        'wait_seconds' => 3
    ],
];
```



## Usage


1. send sms

    ```php
    try {
        $bizId = SendSms::to('SMS_123456', '18688886666', [
            'code' => mt_rand(1000, 9999)
        ]);
    } catch(\Exception $e) {
        echo $e->getMessage();
    }
    ```

2. query sms 

    ```php 
    $page = 1;
    $pageSize = 1;
    $bizId = null;
    $result = QuerySms::search('18688886666', '20180516', $page, $pageSize, $bizId);
    ```

    response
    
    ```json
    {
        "page": 1,
        "pageSize": 1,
        "sendDate": "20180516",
        "result": [{
            "SendDate": "2018-05-16 16:04:53",
            "SendStatus": 3,
            "ReceiveDate": "2018-05-16 16:04:57",
            "ErrCode": "0",
            "TemplateCode": "SMS_123456",
            "Content": "",
            "PhoneNum": "18688886666"
        }],
        "total": 7
    }
    ```

3. send batch sms

    ```php 
        try {
            $bizId = SendSmsBatch::to(
                        'SMS_123456', 
                        [
                            '18688886666',
                            '18666666666'
                        ], 
                        [
                            'sign-1', 'sign-2'
                        ],
                        [
                            [
                                'code' => mt_rand(1000, 9999)
                            ],
                            [
                                'code' => mt_rand(1000, 9999)
                            ]
                        ]
                    );
        } catch (\Exception $e) {
            echo $e->getMessage();
        }
    ```

4. MNS

    see: https://help.aliyun.com/document_detail/55500.html

    ```php 
    /**
     * 回调
     * @param stdClass $message 消息数据
     * @return bool 返回true，则工具类自动删除已拉取的消息。返回false，消息不删除可以下次获取
     */
    SmsQueue::up(function($message) {
    
        /*
        {
          "dest_code": "2199787"
          "send_time": "2018-05-16 18:05:13"
          "sign_name": "signname"
          "sp_id": null
          "sequence_id": 531571249
          "phone_number": "18688886666"
          "content": "回复测试"
        }
        */
        print_r($message);
    
        return true;
    });
    ```

    ```php 
    /**
     * 回调
     * @param stdClass $message 消息数据
     * @return bool 返回true，则工具类自动删除已拉取的消息。返回false，消息不删除可以下次获取
     */
    SmsQueue::report(function($message) {
        
        /*
        {
          "send_time": "2018-05-16 14:18:57"
          "report_time": "2018-05-16 14:19:02"
          "success": true
          "err_msg": "用户接收成功"
          "err_code": "DELIVERED"
          "phone_number": "18688886666"
          "sms_size": "1"
          "biz_id": "48490846451537371^0"
          "out_id": null
        }
        */
        print_r($message);
    
        return true;
    });
    ```

5. set config at runtime

    ```php 
        try {
            $bizId = SendSms::setRuntimeConfig([
                'access_key_id' => 'id',
                'access_key_secret' => 'key'
            ])->to('SMS_123456', '18688886666', [
                'code' => mt_rand(1000, 9999)
            ]);
        } catch(\Exception $e) {
            echo $e->getMessage();
        }
    ```

## Todo

