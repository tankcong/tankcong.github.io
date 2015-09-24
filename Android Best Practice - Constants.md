### Notes: [Consider static factory method](https://medium.com/@orhanobut/consider-static-factory-method-8c0d4eaa5e73)
以下代码在日常开发中经常遇见：

```
public class LoginActivity extends Activity {
    private static final String KEY_USER = "KEY_USER";

    void displayHome() {
        Intent intent = new Intent(this, HomeActivity.class);
        intent.putExtra(KEY_USER, intent);
        startActivity(intent);
    }
}
```

#### 如何保存诸如上面代码的各种`KEY_USER`？  

* 每个Fragment，Activity分别创建相同的KEY  
缺点：违反**DRY**。

* 使KEY变量全局可访问，`LoginActivity.KEY_USER`  
缺点：不必要的耦合。

* 把所有的KEY存在同一个地方，`Constants.java`  
缺点：更大范围的耦合。

#### Solution：  
**Instead of creating the object of another class, let the corresponding class to create the object.**

```
public class HomeActivity extends Activity {
    private static final String KEY_USER = "KEY_USER";

    public static Intent newIntent(Context context, User user) {
        Intent intent = new Intent(context, HomeActivity.class);
        intent.putExtra(KEY_USER, user);
        return intent;
    }

    @Override
    public void onCreate(Bundle savedInstance) {
        User user = getIntent().getParcelableExtra(KEY_USER);
    }
}
```

让`HomeActivity`自己创建自己的`Intent`。同样适用于`Fragment`等的创建及跳转。

#### Conclusion
对于这种简单情况，使用所谓`newIntent()`的技巧确实有很多好处：

* 简洁的代码
* 低耦合
* 方便做创建之前的数据验证
* 方便使用单例
* 方便利用多态（返回子类）

那么对于Preference类的其他KEY，目前看来大多程序还是使用统一`Constants.java`这种形式保存。待研究后填坑。