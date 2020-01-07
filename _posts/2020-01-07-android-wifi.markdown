---
layout: post
title:  "[Android Studio] Android WiFi 설정"
date:   2020-01-07 12:30:00 +0900
categories: android
comments: true
background: '/img/posts/06.jpg'
---

# **WiFi 설정**

<br>

## **AndroidManifest.xml**

```java
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
```

## **MainActivity.java**

```java
package example.pnc.msshin.wifisample;

import android.app.Activity;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.net.NetworkInfo;
import android.net.wifi.ScanResult;
import android.net.wifi.SupplicantState;
import android.net.wifi.WifiConfiguration;
import android.net.wifi.WifiInfo;
import android.net.wifi.WifiManager;
import android.os.Bundle;
import android.text.TextUtils;
import android.util.Log;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends Activity {
    private final String TAG = getClass().getSimpleName();
    private WifiManager mWifiManager;
    private ArrayList<WifiAPItem> mWifiList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        init();
    }

    private void init(){
        mWifiManager = (WifiManager) getApplicationContext().getSystemService(Context.WIFI_SERVICE);
    }

    @Override
    protected void onResume() {
        super.onResume();
        registerWifi();
        if (mWifiManager != null && mWifiManager.isWifiEnabled()){
            mWifiManager.startScan();
        }
    }

    @Override
    protected void onPause() {
        super.onPause();
        unregisterWifi();
    }

    private void registerWifi() {
        IntentFilter filter1 = new IntentFilter();
        filter1.addAction(WifiManager.SCAN_RESULTS_AVAILABLE_ACTION);
        filter1.addAction(WifiManager.RSSI_CHANGED_ACTION);
        registerReceiver(wifiReceiver, filter1);

        IntentFilter filter2 = new IntentFilter();
        filter2.addAction(WifiManager.SUPPLICANT_STATE_CHANGED_ACTION);
        filter2.addAction(WifiManager.NETWORK_STATE_CHANGED_ACTION);
        registerReceiver(wifiConnectionStateReceiver, filter2);
    }

    private void unregisterWifi() {
        unregisterReceiver(wifiReceiver);
        unregisterReceiver(wifiConnectionStateReceiver);
    }

    private final BroadcastReceiver wifiReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            if (intent != null && intent.getAction() != null){
                if (intent.getAction().equals(WifiManager.SCAN_RESULTS_AVAILABLE_ACTION)){
                    Log.d(TAG, "wifiReceiver onReceive - SCAN_RESULTS_AVAILABLE_ACTION");
                }else if (intent.getAction().equals(WifiManager.RSSI_CHANGED_ACTION)){
                    Log.d(TAG, "wifiReceiver onReceive - RSSI_CHANGED_ACTION");
                }

                mWifiList = getMakeWifiList();
                if (mWifiList != null && !mWifiList.isEmpty()){
                     Log.d(TAG, "Detected wifi list length: "+mWifiList.size());
                }
            }
        }
    };

    private BroadcastReceiver wifiConnectionStateReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            String action  = intent.getAction();
            if (action != null) {
                if (action.equals(WifiManager.SUPPLICANT_STATE_CHANGED_ACTION)) {
                    SupplicantState state = intent.getParcelableExtra(WifiManager.EXTRA_NEW_STATE);
                    switch (state) {
                        case ASSOCIATED: Log.i(TAG, "[WiFiState] ASSOCIATED"); break;
                        case ASSOCIATING: Log.i(TAG, "[WiFiState] ASSOCIATING"); break;
                        case AUTHENTICATING: Log.i(TAG, "[WiFiState] Authenticating..."); break;
                        case COMPLETED: Log.i(TAG, "[WiFiState] Connected"); break;
                        case DISCONNECTED: Log.i(TAG, "[WiFiState] Disconnected"); break;
                        case DORMANT: Log.i(TAG, "[WiFiState] DORMANT"); break;
                        case FOUR_WAY_HANDSHAKE: Log.i(TAG, "[WiFiState] FOUR_WAY_HANDSHAKE"); break;
                        case GROUP_HANDSHAKE: Log.i(TAG, "[WiFiState] GROUP_HANDSHAKE"); break;
                        case INACTIVE: Log.i(TAG, "[WiFiState] INACTIVE"); break;
                        case INTERFACE_DISABLED: Log.i(TAG, "[WiFiState] INTERFACE_DISABLED"); break;
                        case INVALID: Log.i(TAG, "[WiFiState] INVALID"); break;
                        case SCANNING: Log.i(TAG, "[WiFiState] SCANNING"); break;
                        case UNINITIALIZED: Log.i(TAG, "[WiFiState] UNINITIALIZED"); break;
                        default: Log.i(TAG, "[WiFiState] Unknown"); break;
                    }
                } else if (action.equals(WifiManager.NETWORK_STATE_CHANGED_ACTION)) {
                    NetworkInfo netInfo = intent.getParcelableExtra(WifiManager.EXTRA_NETWORK_INFO);
                    WifiInfo wifiInfo = intent.getParcelableExtra(WifiManager.EXTRA_WIFI_INFO);
                    if (netInfo != null) Log.d(TAG, "[NetState] netInfo : "+netInfo.toString());
                    if (wifiInfo != null) Log.d(TAG, "[NetState] wifiInfo : "+wifiInfo.toString());
                }
            }
        }
    };

    public static String convertToQuotedString(String string) {
        if (TextUtils.isEmpty(string)) {
            return "";
        }

        final int lastPos = string.length() - 1;
        if(lastPos > 0 && (string.charAt(0) == '"' && string.charAt(lastPos) == '"')) {
            return string;
        }

        return "\"" + string + "\"";
    }

    private WifiConfiguration getWifiConfiguration(String bssid, String ssid) {
        if (mWifiManager != null) {
            List<WifiConfiguration> wcList = mWifiManager.getConfiguredNetworks();
            if (wcList != null) {
                for (WifiConfiguration wc : wcList){
                    if (wc == null) return null;
                    if (wc.BSSID == null && wc.SSID.equals(convertToQuotedString(ssid))) {
                        Log.d(TAG, "getWifiConfiguration() Find!! : SSID=" + wc.SSID);
                        return wc;
                    } else if (wc.BSSID != null && wc.BSSID.equals(bssid) && wc.SSID.equals(convertToQuotedString(ssid))) {
                        Log.d(TAG, "getWifiConfiguration() Find!! : BSSID=" + wc.BSSID + ", SSID=" + wc.SSID);
                        return wc;
                    } else if (wc.BSSID != null && wc.BSSID.equals("any") && wc.SSID.equals(convertToQuotedString(ssid))) {
                        Log.d(TAG, "getWifiConfiguration() Find!! : BSSID=" + wc.BSSID + ", SSID=" + wc.SSID);
                        return wc;
                    }
                }
            }
        }

        return null;
    }

    private ArrayList<WifiAPItem> getMakeWifiList() {
        ArrayList<WifiAPItem> wifiArr = new ArrayList<>();
        boolean isEnabled = false;
        if (mWifiManager != null){
            isEnabled = mWifiManager.isWifiEnabled();
        }
        Log.d(TAG, "makeWifiList() - enabled : "+isEnabled);
        if (isEnabled) {
            String[] connectedInfo = getConnectedInfo();

            for (ScanResult result : mWifiManager.getScanResults()) {
                WifiAPItem adapterItem = new WifiAPItem(
                        result.BSSID,
                        result.SSID,
                        result.capabilities,
                        result.frequency,
                        result.level,
                        getWifiConfiguration(result.BSSID, result.SSID),
                        result,
                        WifiAPItem.STATE_NONE
                );

                if (connectedInfo != null && connectedInfo.length > 1 && connectedInfo[1] != null && connectedInfo[1].equals(convertToQuotedString(adapterItem.getSsid()))) {
                    Log.d(TAG, "Find connected AP ! : insert first item");
                    adapterItem.setState(WifiAPItem.STATE_CONNECTED);
                } else if (adapterItem.getWc() != null) {
                    adapterItem.setState(WifiAPItem.STATE_SAVED);
                } else {
                    adapterItem.setState(WifiAPItem.STATE_NONE);
                }
                Log.d(TAG, "AP - " + adapterItem.toString());
                wifiArr.add(adapterItem);
            }
        }
        return wifiArr;
    }

    private String[] getConnectedInfo() {
        if( mWifiManager == null ) return null;

        String[] info = new String[2];
        WifiInfo connectionInfo = mWifiManager.getConnectionInfo();
        if(connectionInfo != null) {
            info[0] = connectionInfo.getBSSID();
            info[1] = connectionInfo.getSSID();
            Log.d(TAG, "getConnectedInfo() : BSSID=" + connectionInfo.getBSSID() + ", SSID=" + connectionInfo.getSSID());
        }

        return info;
    }
}
```

## **WifiAPItem.java**

```java
package example.pnc.msshin.wifisample;

import android.net.wifi.ScanResult;
import android.net.wifi.WifiConfiguration;
import android.net.wifi.WifiManager;
import android.util.Log;


public class WifiAPItem {
    private static String TAG = "WiFiAPItem";
    public static final int SECURITY_NONE = 0;
    public static final int SECURITY_WEP = 1;
    public static final int SECURITY_PSK = 2;
    public static final int SECURITY_EAP = 3;
    public static final int STATE_NONE = 0;
    public static final int STATE_SAVED = 1;
    public static final int STATE_CONNECTING = 2;
    public static final int STATE_AUTHENTICATING = 3;
    public static final int STATE_CONNECTED = 4;
    private enum PskType {UNKNOWN, WPA, WPA2, WPA_WPA2}
    private String bssid;
    private String ssid;
    private String capabilities;
    private int frequency;
    private int level;
    private int security;
    private WifiConfiguration wc;
    private ScanResult scanResult;
    private int state;
    private PskType pskType;

    public WifiAPItem(){
        state = STATE_NONE;
        pskType = PskType.UNKNOWN;
    }

    public WifiAPItem(String bssid, String ssid, String capabilities, int frequency, int level, WifiConfiguration wc, ScanResult scanResult, int state){
        this.state = STATE_NONE;
        this.pskType = PskType.UNKNOWN;

        setBssid(bssid);
        setSsid(ssid);
        setCapabilities(capabilities);
        setFrequency(frequency);
        setLevel(level, true, 5);
        setSecurity(scanResult);
        setWc(wc);
        setScanResult(scanResult);
        setState(state);
        setPskType(scanResult);
    }

    public String getBssid() {
        return bssid;
    }

    public void setBssid(String bssid) {
        this.bssid = bssid;
    }

    public String getSsid() {
        return ssid;
    }

    public void setSsid(String ssid) {
        this.ssid = ssid;
    }

    public String getCapabilities() {
        return capabilities;
    }

    public void setCapabilities(String capabilities) {
        this.capabilities = capabilities;
    }

    public int getFrequency() {
        return frequency;
    }

    public void setFrequency(int frequency) {
        this.frequency = frequency;
    }

    public int getLevel() {
        return level;
    }

    public void setLevel(int level, boolean isConvert, int convertMaxLevel) {
        if (isConvert){
            WifiManager.calculateSignalLevel(level, convertMaxLevel);
        }else {
            this.level = level;
        }
    }

    public int getSecurity() {
        return security;
    }

    public void setSecurity(ScanResult scanResult) {
        if (scanResult != null) {
            security = getSecurity(scanResult);
        }
    }

    public WifiConfiguration getWc() {
        return wc;
    }

    public void setWc(WifiConfiguration wc) {
        this.wc = wc;
    }

    public ScanResult getScanResult() {
        return scanResult;
    }

    public void setScanResult(ScanResult scanResult) {
        this.scanResult = scanResult;
    }

    private int getSecurity(ScanResult result) {
        if (result.capabilities.contains("WEP")) {
            return SECURITY_WEP;
        } else if (result.capabilities.contains("PSK")) {
            return SECURITY_PSK;
        } else if (result.capabilities.contains("EAP")) {
            return SECURITY_EAP;
        }
        return SECURITY_NONE;
    }

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
    }

    public PskType getPskType() {
        return pskType;
    }

    public void setPskType(ScanResult scanResult) {
        if (security == SECURITY_PSK) {
            this.pskType = getPskType(scanResult);
        }
    }

    private PskType getPskType(ScanResult result) {
        boolean wpa = result.capabilities.contains("WPA-PSK");
        boolean wpa2 = result.capabilities.contains("WPA2-PSK");
        if (wpa2 && wpa) {
            return PskType.WPA_WPA2;
        } else if (wpa2) {
            return PskType.WPA2;
        } else if (wpa) {
            return PskType.WPA;
        } else {
            Log.w(TAG, "Received abnormal flag string: " + result.capabilities);
            return PskType.UNKNOWN;
        }
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("bssid: ").append(bssid);
        sb.append(", ssid: ").append(ssid);
        sb.append(", capabilities: ").append(capabilities);
        sb.append(", frequency: ").append(frequency);
        sb.append(", level: ").append(level);
        sb.append(", security: ").append(security);
        sb.append(", state: ").append(state);
        sb.append(", pskType: ").append(pskType);
        return sb.toString();
    }
}
```