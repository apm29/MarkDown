## LiveData
___
#### 1. ViewModel
* 用于管理`Activity/Fragment`的数据管理,并且能处理`Activity/Fragment`以及其他部件的通讯

*  `ViewModel`必须由Fragment/Activity(`scope`)创建,只要`Scope`存活就会一直保留

*  所以说`ViewModel`不会因为屏幕旋转等configuration change导致回收,新的`Activity/Fragment`实例会重新连接到原有的`ViewModel`

* ` ViewModel`存在的目的是为了获得和保存`Activity/Fragment`所必须的信息,`Activity/Fragment`能够观察到`ViewModel`内部数据的改变.通常使用`LiveData`或者`DataBinding`实现,或者其他可观察`Observability`的概念

* `ViewModel`的唯一用途就是管理UI更新需要的数据,它不应该触及View树或者持有`scope`的引用

* 典型用法
  Activity:
  ```java
  public class UserActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.user_activity_layout);
        final UserModel viewModel = ViewModelProviders.of(this).get(UserModel.class);
        viewModel.userLiveData.observer(this, new Observer<User>() {
           {@literal @}Override
            public void onChanged(@Nullable User data) {
                // update ui.
            }
        });
        findViewById(R.id.button).setOnClickListener(new View.OnClickListener() {
            {@literal @}Override
            public void onClick(View v) {
                 viewModel.doAction();
            }
        });
    }
}
```

ViewModel:
```java
public class UserModel extends ViewModel {
    public final LiveData<User>userLiveData = new LiveData<>();
    public UserModel() {
        // trigger user load.
    }
    void doAction() {
        // depending on the action, do necessary business logic calls and update the
        // userLiveData.
    }
}
```

* ViewModel也可以用于通讯层(同一Activity管理的Fragment之间),每个Fragment可以通过Activity的Key获得同一个`ViewModel`实例

```java
public class MyFragment extends Fragment {
    public void onStart() {
        UserModel userModel = ViewModelProviders.of(getActivity()).get(UserModel.class);
    }
}
```
