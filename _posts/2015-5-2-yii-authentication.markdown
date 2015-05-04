---
layout: post
title:  "Yii框架的登录验证"
date:   2015-05-02
categories: php, yii
---

主要文件是/protected/UserIdentity.php

默认的代码是这样的.

    class UserIdentity extends CUserIdentity
    {
        public $user;
        public $errcode;
        /**
         * Authenticates a user.
         * The example implementation makes sure if the username and password
         * are both 'demo'.
         * In practical applications, this should be changed to authenticate
         * against some persistent user identity storage (e.g. database).
         * @return boolean whether authentication succeeds.
         */
        public function authenticate()
        {
            $user = BjUser::model()->findByAttributes(array('tel'=>$this->username));

            if($user)
            {
                if($user->password === md5($this->password))
                {
                    $user->lastlogin_time = time();
                    $user->save();

                    $this->user = $user;
                }
                else
                {
                    $this->errorCode=self::ERROR_PASSWORD_INVALID;
                }
            }
            else
            {
                $this->errorCode=self::ERROR_USERNAME_INVALID;
            }

            return !$this->errorCode;
            
        }

        public function getUser()
        {
            return $this->user;
        }
    }

在登录Controller中调用.

        $_identity = new UserIdentity($username, $password);

        $_identity->authenticate();

        if($_identity->errorCode===UserIdentity::ERROR_NONE)
        {
            $result['data'] = $_identity->getUser();
            $result['success'] = true;

        }else{
            $result['success'] = false;

        }

注册登录的时候使用md5()方法对password字段进行加密,解密.

    $password = md5($_POST['password']);