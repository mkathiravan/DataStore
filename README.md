# DataStore

DataStore is a new and improved data storage solution aimed at replacing SharedPreferences. Built on Kotlin coroutines and Flow, DataStore provides two different implementations: Proto DataStore, that lets you store typed objects (backed by protocol buffers) and Preferences DataStore, that stores key-value pairs. Data is stored asynchronously, consistently, and transactionally, overcoming some of the drawbacks of SharedPreferences.


    package com.plcoding.datastoreandroid

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import androidx.datastore.core.DataStore
    import androidx.datastore.createDataStore
    import androidx.datastore.preferences.core.Preferences
    import androidx.datastore.preferences.core.edit
    import androidx.datastore.preferences.core.preferencesKey
    import androidx.datastore.preferences.createDataStore
    import androidx.lifecycle.lifecycleScope
    import com.plcoding.datastoreandroid.databinding.ActivityMainBinding
    import kotlinx.coroutines.flow.first
    import kotlinx.coroutines.launch

    class MainActivity : AppCompatActivity() {

    private var _binding: ActivityMainBinding? = null
    private val binding get() = _binding!!

    private lateinit var dataStore: DataStore<Preferences>

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        _binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        dataStore = createDataStore(name = "settings")

        binding.btnSave.setOnClickListener {
            lifecycleScope.launch {
                save(
                        binding.etSaveKey.text.toString(),
                        binding.etSaveValue.text.toString()
                )
            }
        }

        binding.btnRead.setOnClickListener {
            lifecycleScope.launch {
                val value = read(binding.etReadkey.text.toString())
                binding.tvReadValue.text = value ?: "No value found"
            }
        }
    }

# To save data into the preferences

    private suspend fun save(key: String, value: String) {
        val dataStoreKey = preferencesKey<String>(key)
        dataStore.edit { settings ->
            settings[dataStoreKey] = value
        }
    }
# To Read data from the preferences

    private suspend fun read(key: String): String? {
        val dataStoreKey = preferencesKey<String>(key)
        val preferences = dataStore.data.first()
        return preferences[dataStoreKey]
    }

    override fun onDestroy() {
        super.onDestroy()
        _binding = null
    }
    }
    
    
 ![image](https://user-images.githubusercontent.com/39657409/102799811-ffe31080-43d8-11eb-92df-274db21d9a20.png)
   
