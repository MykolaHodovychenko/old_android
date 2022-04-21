# Фрагменты в Android

План занятия:

1. Зачем были введены фрагменты
2. Фрагменты для телефонов и планшетов

## Понятие и назначение фрагментов

Фрагмент (класс `Fragment`) отвечает за внешний вид и поведение части пользовательского интерфейса в Activity (очень условно их можно сопоставить с фреймами в HTML). Фрагмент можно рассматривать как модульную часть Activity.

Разработчик может объединить несколько фрагментов в одно Activity для построения многопанельного UI и повторного использования фрагментов в нескольких Activity.

Кроме этого, фрагменты обладают динамическим поведением – их можно добавлять или удалять непосредственно во время выполнения операции.

К примеру, если ваше приложение открывается на телефонах, то на экране показывается список и нижнее меню. На планшетах, это же окно может быть показано как меню слева и список справа.

<p align="center">
  <img src="img/img_01.png" />
</p>

Фрагмент всегда должен быть встроен в Activity, и на его жизненный цикл напрямую влияет жизненный цикл Activity. Например, когда Activity приостановлена, в том же состоянии находятся и все фрагменты внутри нее, а когда Activity уничтожается, уничтожаются и все фрагменты.

Фрагменты появились с версии Android 3.0 для обеспечения гибкости и динамичности UI на больших экранах (планшеты, Android TV). На большом экране существует гораздо больше возможностей для объединения и перестановки компонентов пользовательского интерфейса, что и позволяют делать фрагменты. 

## Создание и добавление фрагмента в Activity

Для использования фрагментов, необходимо добавить в `build.gradle` зависимость

```groovy
dependencies {
    implementation 'androidx.fragment:fragment-ktx:1.4.1'
}
```

Создадим макет `fragment_first.xml` и `fragment_second.xml` для фрагментов

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:background="#fb8500"
    android:layout_height="match_parent">

</androidx.constraintlayout.widget.ConstraintLayout>
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:background="#023047"
    android:layout_height="match_parent">

</androidx.constraintlayout.widget.ConstraintLayout>
```

Создаем класс фрагмента `FragmentFirst.kt` и `FragmentSecond.kt`

```kotlin
class FragmentFirst : Fragment(R.layout.fragment_first)
```

```kotlin
class SecondFragment : Fragment(R.layout.fragment_second)
```

### Добавление фрагмента в Activity

Добавить фрагмент в Activity можно двумя способами:

- добавить фрагмент в макет Activity;
- добавить контейнер для фрагментов в Activity и после этого программно добавить фрагмент в контейнер.

#### Добавление фрагмента в макет

Добавим в макет Activity два фрагмента

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/container_first"
        android:name="ua.edu.op.fragmentsone.FirstFragment"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toTopOf="@+id/guideline3"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/container_second"
        android:name="ua.edu.op.fragmentsone.SecondFragment"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/container_first" />

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guideline3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_percent="0.5" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Атрибут `android:name` определяет класс фрагмента. При "надувании" макета, будет создан объект фрагмента.

#### Добавление фрагмента через контейнер

В качестве контейнера используется тот же элемент `FragmentContainerView`, но без указания класса фрагмента, но необходимо добавить атрибут `android:id` для работы с контейнером в коде Activity.

Уберем из макета атрибут `android:name`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/container_first"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toTopOf="@+id/guideline3"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/container_second"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/container_first" />

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guideline3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_percent="0.5" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

При добавлении фрагментов программно, ими можно манипулировать - добавлять, удалять, менять фрагмент в контейнере. Для этого используется объект `FragmentManager`, который может быть использован для создания `FragmentTransaction`.

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        if (savedInstanceState == null) {
            supportFragmentManager.commit {
                setReorderingAllowed(true)
                add<FirstFragment>(R.id.container_first)
                add<SecondFragment>(R.id.container_second)
            }
        }
    }
}
```

Обратите внимание, что добавление фрагмента происходит только когда `savedInstanceState` равен `null`. Это условие позволяет убедиться, что добавление фрагментов происходит только единожды, когда Activity создается впервые. Когда Activity воссоздается, фрагменты заново не добавляются, так как фрагменты будут автоматически воссозданы операционной системой.

Метод `setReorderingAllowed(true)` оптимизирует изменение состояний для корректной работы анимаций и переходов.

## Использование виджета ViewPager и TabLayout совместно с фрагментами

Фрагменты часто используют вместе с такими виджетами как ViewPager2 и TabLayout. Создадим новый проект и попробуем реализовать приложение с данными виджетами.

Создадим класс для фрагмента

```kotlin
class ThirdFragment : Fragment(R.layout.fragment_first)
```

Создадим макет для фрагмента

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/page_number"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Page #"
        android:textAppearance="@style/TextAppearance.MaterialComponents.Headline3"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

После этого добавим в макет MainActivity виджет ViewPager2 и укажем для этого виджета id

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewpager"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginBottom="8dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

Создадим класс адаптера для ViewPager2. В рамках данного приложения, класс адаптера будет внутренним классом для MainActivity.

Класс адаптера наследуется от FragmentStateAdapter, в конструктор которого мы должны передать объект Activity, куда будут "крепиться" фрагменты.

```kotlin
class MainActivity : AppCompatActivity() {
    
    ...

    class MyAdapter(activity: AppCompatActivity) : FragmentStateAdapter(activity) {
        override fun getItemCount() = 5
        override fun createFragment(position: Int) = ThirdFragment.newInstance(position)
    }
}
```

Нам необходимо реализовать два метода - метод `getItemCount()`, который возвращает количество объектов фрагмента в `ViewPager2` и метод `createFragment()`, который возвращает объект i-го фрагмента в `ViewPager2`.

Создадим в классе фрагмента метод для получения объекта фрагмента.

```kotlin
class ThirdFragment : Fragment(R.layout.fragment_first) {

    ...

    companion object {
        fun newInstance(position: Int): ThirdFragment {
            val args = Bundle()
            args.putInt("position", position);

            val fragment = ThirdFragment()
            fragment.arguments = args
            return fragment
        }
    }
}
```

Обратите внимание, что мы передаем в объект фрагмента его порядковый номер в `ViewPager2`. Далее мы должны присвоить этот порядковый номер текстовом полю.

Для этого нам нужно переопределить методы `onCreateView()` и `onViewCreated()`. Метод `onCreateView()` вызывается раньше и мы должны предоставить "надутый" макет фрагмента в качестве результата работы методы. Метод `onViewCreated()` вызывается после добавления макета, в этом методе мы можем манипулировать элементами UI.

```kotlin
class ThirdFragment : Fragment(R.layout.fragment_first) {

    private lateinit var binding: FragmentThirdBinding

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = FragmentThirdBinding.inflate(layoutInflater,container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        binding.pageNumber.text = arguments?.getInt("position",-1).toString()
    }

    companion object {
        fun newInstance(position: Int): ThirdFragment {
            val args = Bundle()
            args.putInt("position", position);

            val fragment = ThirdFragment()
            fragment.arguments = args
            return fragment
        }
    }
}
```

Виджет TabLayout можно использовать в связке с ViewPager2 или отдельно.

Добавим TabLayout в макет MainActivity

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">


    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewpager"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/tabs" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

Далее необходимо создать объект класса TabLayoutMediator и в конструкторе передать ему объект TabLayout, объект ViewPager и лямбду, которая будет содержать код для установки текста и стиля для закладок.

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.viewpager.adapter = MyAdapter(this)

        TabLayoutMediator(binding.tabs, binding.viewpager) { tab, pos ->
            tab.text = "PAGE ${pos}"
        }.attach()
    }

    class MyAdapter(activity: AppCompatActivity) : FragmentStateAdapter(activity) {

        override fun getItemCount() = 5
        override fun createFragment(position: Int) = ThirdFragment.newInstance(position)
    }
}
```

## Использование BottomNavigationView

Создадим макет для MainActivity

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">


    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragmentContainerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintBottom_toTopOf="@+id/bottomNavigationView"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottomNavigationView"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:menu="@menu/bottom_menu" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

Создадим меню для BNV

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/item_1"
        android:enabled="true"
        android:icon="@drawable/ic_home"
        android:title="Home" />
    <item
        android:id="@+id/item_2"
        android:enabled="true"
        android:icon="@drawable/ic_list"
        android:title="List" />
    <item
        android:id="@+id/item_3"
        android:enabled="true"
        android:icon="@drawable/ic_user"
        android:title="User" />
</menu>
```

Пропишем в коде MainActivity слушатель для BNV

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.bottomNavigationView.setOnItemSelectedListener {
            when(it.itemId) {
                R.id.item_1 -> setCurrentFragment(0)
                R.id.item_2 -> setCurrentFragment(1)
                R.id.item_3 -> setCurrentFragment(2)
            }
            true
        }

        binding.bottomNavigationView.selectedItemId = R.id.item_1
    }

    private fun setCurrentFragment(pos: Int) {
        supportFragmentManager.commit {
            replace(R.id.fragmentContainerView, ThirdFragment.newInstance(pos))
        }
    }
}
```