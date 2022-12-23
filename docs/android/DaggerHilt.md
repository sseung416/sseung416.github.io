---
layout: default
title: RecyclerView
parent: Android
---

# Dagger Hilt

---

### context를 주입받는 법

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object DatabaseModule {
    
    @Singleton
    @Provides
    fun providesAppDatabase(@AppicationContext context: Context): AppDatabase =
        Room.databaseBuilder(context, AppDatabse::class.java, "test.db").build()
}
```