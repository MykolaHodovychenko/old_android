# **Cписковый элемент RecyclerView**



В Android, начиная с версии 5.0, доступны новые View-элементы экрана, которые упрощают работу со списками: **RecyclerView** и **CardView**. С помощью этих компонентов приложение будет выглядеть и вести себя в соответствии с рекомендациями Google Material Design.



![img](assets%2F-LUXv2U9nKmeKg3yJCdg%2F-LWvPMk-P23f_taveKQ1%2F-LWvPNPpsN1fxirQVsNc%2Fimage-1549558844683.png)



> **Android Support Library** - набор библиотек, который позволяет использовать нововведения платформы Android в более старых версиях операционной системы.
>
> Подробнее - <https://developer.android.com/topic/libraries/support-library/>



```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="8dp">

    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:cardCornerRadius="8dp"
        app:cardElevation="8dp">


        <android.support.constraint.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <Button
                android:id="@+id/button2"
                style="@android:style/Widget.DeviceDefault.Button"
                android:layout_width="wrap_content"
                android:layout_height="0dp"
                android:layout_marginEnd="8dp"
                android:text="Button"
                app:layout_constraintBottom_toBottomOf="@+id/imageView"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintTop_toTopOf="@+id/imageView" />

            <ImageView
                android:id="@+id/imageView"
                android:layout_width="64dp"
                android:layout_height="64dp"
                android:layout_marginStart="8dp"
                android:layout_marginTop="8dp"
                android:layout_marginBottom="8dp"
                android:scaleType="centerInside"
                app:layout_constraintBottom_toBottomOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                tools:srcCompat="@tools:sample/avatars[11]" />

            <TextView
                android:id="@+id/textView"
                android:layout_width="0dp"
                android:layout_height="0dp"
                android:layout_marginStart="8dp"
                android:layout_marginEnd="8dp"
                android:gravity="center"
                android:text="TextView"
                android:textAppearance="@style/TextAppearance.AppCompat.Display1"
                app:layout_constraintBottom_toBottomOf="@+id/imageView"
                app:layout_constraintEnd_toStartOf="@+id/button2"
                app:layout_constraintStart_toEndOf="@+id/imageView"
                app:layout_constraintTop_toTopOf="@+id/imageView" />
        </android.support.constraint.ConstraintLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```