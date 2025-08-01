// MainActivity.java
package com.example.cryptopanic;

import android.os.Bundle;
import android.os.Handler;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

import org.json.JSONObject;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class MainActivity extends AppCompatActivity {

    TextView cryptoPrice, signalView;
    Handler handler = new Handler();
    String BINANCE_URL = "https://api.binance.com/api/v3/ticker/price?symbol=BTCUSDT";

    double[] priceHistory = new double[20];
    int index = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        cryptoPrice = findViewById(R.id.cryptoPrice);
        signalView = findViewById(R.id.signalView);

        startPriceUpdates();
    }

    void startPriceUpdates() {
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                fetchPrice();
                handler.postDelayed(this, 5000); // update every 5 seconds
            }
        }, 1000);
    }

    void fetchPrice() {
        new Thread(() -> {
            try {
                URL url = new URL(BINANCE_URL);
                HttpURLConnection conn = (HttpURLConnection) url.openConnection();
                conn.setRequestMethod("GET");

                BufferedReader in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                String inputLine;
                StringBuilder content = new StringBuilder();
                while ((inputLine = in.readLine()) != null) {
                    content.append(inputLine);
                }

                in.close();
                conn.disconnect();

                JSONObject json = new JSONObject(content.toString());
                double price = json.getDouble("price");

                runOnUiThread(() -> {
                    cryptoPrice.setText("BTC Price: $" + price);
                    String signal = calculateSignal(price);
                    signalView.setText("Signal: " + signal);
                });

            } catch (Exception e) {
                e.printStackTrace();
            }
        }).start();
    }

    String calculateSignal(double price) {
        // Store price in history
        priceHistory[index % priceHistory.length] = price;
        index++;

        if (index > 10) {
            double oldPrice = priceHistory[(index - 10) % priceHistory.length];
            double dropPercent = (oldPrice - price) / oldPrice;
            if (dropPercent >= 0.05) {
                return "BUY 📈 (Panic Drop)";
            }
        }
        return "HOLD";
    }
}