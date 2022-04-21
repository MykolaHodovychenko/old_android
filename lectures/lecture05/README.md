# Списки в Android 

## ListView

```groovy
implementation 'com.github.javafaker:javafaker:1.0.2'
```

MainActivity.kt

```kotlin
class MainActivity : AppCompatActivity() {

    private val list: MutableList<User> = mutableListOf()
    private val faker: Faker = Faker()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val adapter = UsersAdapter(list, layoutInflater)
        binding.list.adapter = adapter

        binding.btnAdd.setOnClickListener {
            list.add(User(faker.name().fullName(), faker.job().position()))
            adapter.notifyDataSetChanged()
        }

        binding.btnRemove.setOnClickListener {
            if (list.isNotEmpty())
                list.removeLast()

            adapter.notifyDataSetChanged()
        }

        binding.btnClear.setOnClickListener {
            list.clear()
            adapter.notifyDataSetChanged()
        }
    }

    data class User(val name: String, val job: String)
}
```

UsersAdapter.kt

```kotlin
class UsersAdapter(
    private val list: MutableList<MainActivity.User>,
    private val inflater: LayoutInflater
) :
    BaseAdapter() {

    private class ViewHolder(
        val name: TextView,
        val job: TextView
    )

    @SuppressLint("ViewHolder")
    override fun getView(p0: Int, p1: View?, p2: ViewGroup?): View {

        val convertView = p1 ?: inflater.inflate(R.layout.item_layout, p2, false).also {
            val holder =
                ViewHolder(it.findViewById(R.id.tv_name), it.findViewById(R.id.tv_position))
            it.tag = holder
        }

        (convertView.tag as ViewHolder).also {
            it.name.text = list[p0].name
            it.job.text = list[p0].job
        }

        return convertView
    }

    override fun getCount(): Int = list.size
    override fun getItem(p0: Int): Any = list[p0]
    override fun getItemId(p0: Int): Long = 0
}
```

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btn_add"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Add"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@+id/btn_remove"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent" />

    <Button
        android:id="@+id/btn_remove"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Remove"
        app:layout_constraintBottom_toBottomOf="@+id/btn_add"
        app:layout_constraintEnd_toStartOf="@+id/btn_clear"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toEndOf="@+id/btn_add"
        app:layout_constraintTop_toTopOf="@+id/btn_add" />

    <Button
        android:id="@+id/btn_clear"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="clear"
        app:layout_constraintBottom_toBottomOf="@+id/btn_remove"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toEndOf="@+id/btn_remove"
        app:layout_constraintTop_toTopOf="@+id/btn_remove" />

    <ListView
        android:id="@+id/list"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginBottom="8dp"
        app:layout_constraintBottom_toTopOf="@+id/btn_add"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

list_item.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/tv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:text="Name"
        android:textAppearance="@style/TextAppearance.MaterialComponents.Headline6"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/tv_position"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:text="Position"
        android:textAppearance="@style/TextAppearance.Material3.LabelMedium"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="@+id/tv_name"
        app:layout_constraintTop_toBottomOf="@+id/tv_name" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

## RecyclerView

MainActivity.kt

```kotlin
class MainActivity : AppCompatActivity() {

    private val list: MutableList<User> = mutableListOf()
    private val faker: Faker = Faker()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // val binding = ActivityMainBinding.inflate(layoutInflater)
        val binding = ActivityMainRecyclerBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val adapter = UsersRecyclerAdapter(layoutInflater)
        binding.list.adapter = adapter
        binding.list.layoutManager = LinearLayoutManager(this)
        adapter.submitList(null)

        binding.btnAdd.setOnClickListener {
            val user = User(faker.name().fullName(), faker.job().position())
            list.add(user)

            adapter.submitList(list.toList())
        }

        binding.btnRemove.setOnClickListener {
            if (list.isNotEmpty())
                list.removeLast()

            adapter.submitList(list.toList())
        }

        binding.btnClear.setOnClickListener {
            list.clear()
            adapter.submitList(list.toList())
        }
    }

    data class User(val name: String, val job: String)
}
```

UsersRecyclerAdapter.kt

```kotlin
class UsersRecyclerAdapter(
    private val inflater: LayoutInflater
) :
    ListAdapter<MainActivity.User, UsersRecyclerAdapter.ViewHolder>(UserDiffCallback) {

    class ViewHolder(
        view: View
    ) : RecyclerView.ViewHolder(view) {
        private val name = view.findViewById<TextView>(R.id.tv_name)
        private val job = view.findViewById<TextView>(R.id.tv_position)

        fun bind(user: MainActivity.User) {
            name.text = user.name
            job.text = user.job
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = inflater.inflate(R.layout.item_layout, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val user = getItem(position)
        holder.bind(user)
    }

    object UserDiffCallback : DiffUtil.ItemCallback<MainActivity.User>() {
        override fun areItemsTheSame(
            oldItem: MainActivity.User,
            newItem: MainActivity.User
        ): Boolean {

            val res = oldItem == newItem

            return res
        }

        override fun areContentsTheSame(
            oldItem: MainActivity.User,
            newItem: MainActivity.User
        ): Boolean {

            val res = oldItem.name == newItem.name && oldItem.job == newItem.job

            return res
        }
    }
}
```

Клик на пункт списка

UserActivity.kt

```kotlin
class UserActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding = ActivityUserBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.tvName.text = intent.getStringExtra("name") ?: ""
        binding.tvJob.text = intent.getStringExtra("job") ?: ""
    }
}
```

activity_user.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/tv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:text="Name"
        android:textAppearance="@style/TextAppearance.MaterialComponents.Headline6"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/tv_position"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:text="Position"
        android:textAppearance="@style/TextAppearance.Material3.LabelMedium"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="@+id/tv_name"
        app:layout_constraintTop_toBottomOf="@+id/tv_name" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

MainActivity.kt

```kotlin
class MainActivity : AppCompatActivity() {

    private val faker: Faker = Faker()

    private lateinit var adapter: UsersRecyclerAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding = ActivityMainRecyclerBinding.inflate(layoutInflater)
        setContentView(binding.root)

        adapter = UsersRecyclerAdapter(layoutInflater) {
            val intent = Intent(this, UserActivity::class.java)
            intent.putExtra("name", it.name)
            intent.putExtra("job", it.job)
            startActivity(intent)
        }
        binding.list.adapter = adapter
        binding.list.layoutManager = LinearLayoutManager(this)

        binding.btnAdd.setOnClickListener {
            val user = User(faker.name().fullName(), faker.job().position())
            App.list.add(user)

            adapter.submitList(App.list.toList())
        }

        binding.btnRemove.setOnClickListener {
            if (App.list.isNotEmpty())
                App.list.removeLast()

            adapter.submitList(App.list.toList())
        }

        binding.btnClear.setOnClickListener {
            App.list.clear()
            adapter.submitList(App.list.toList())
        }
    }

    override fun onStart() {
        super.onStart()

        adapter.submitList(App.list.toList())
    }

    data class User(val name: String, val job: String)
}
```

UsersRecyclerAdapter.kt

```kotlin
class UsersRecyclerAdapter(
    private val inflater: LayoutInflater,
    private val onClick: (MainActivity.User) -> Unit
) :
    ListAdapter<MainActivity.User, UsersRecyclerAdapter.ViewHolder>(UserDiffCallback) {

    class ViewHolder(
        view: View,
        val onClick: (MainActivity.User) -> Unit
    ) : RecyclerView.ViewHolder(view) {
        private val name = view.findViewById<TextView>(R.id.tv_name)
        private val job = view.findViewById<TextView>(R.id.tv_position)
        private var user: MainActivity.User? = null

        init {
            view.setOnClickListener {
                user?. let {
                    onClick(it)
                }
            }
        }

        fun bind(user: MainActivity.User) {
            this.user = user
            name.text = user.name
            job.text = user.job
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = inflater.inflate(R.layout.item_layout, parent, false)
        return ViewHolder(view, onClick)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val user = getItem(position)
        holder.bind(user)
    }

    object UserDiffCallback : DiffUtil.ItemCallback<MainActivity.User>() {
        override fun areItemsTheSame(
            oldItem: MainActivity.User,
            newItem: MainActivity.User
        ): Boolean {

            val res = oldItem == newItem

            return res
        }

        override fun areContentsTheSame(
            oldItem: MainActivity.User,
            newItem: MainActivity.User
        ): Boolean {

            val res = oldItem.name == newItem.name && oldItem.job == newItem.job

            return res
        }
    }
}
```