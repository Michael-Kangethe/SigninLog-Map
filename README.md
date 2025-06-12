# 🔐 SigninLog MAP - Visualization of Failed Sign-In Attempts

## 🧩 SigninLog Map json Source Code

[Signin-Failure.json] 

## 📌 Overview

The **SigninLog MAP** is a visualization tool designed to plot failed sign-in attempts across virtual machines on an interactive geographic map. It leverages Azure Monitor Workbooks and Kusto Query Language (KQL) to extract and map the data.


![SigninLog Map](https://github.com/user-attachments/assets/a0bf1e81-c2c6-4abf-ae89-c6b1cd98897f)

---

## 🧭 Purpose

This visualization provides a quick and insightful view into potential unauthorized access attempts by:

- Mapping failed login attempts by IP location.
- Highlighting regions with high attack frequency.
- Helping security teams detect suspicious login patterns.

---

## ⚙️ Customization Features

The map supports full customization via built-in map settings in Azure Workbooks.

![Map Settings](https://github.com/user-attachments/assets/e6b48221-474f-410a-aa6b-fbdfdd5358ac)


### Available Options:
- 🎨 **Color Coding**: Use a heatmap (e.g., green-red) to reflect attempt intensity.
- 📍 **Metric Labels**: Customize labels (e.g., city, country, or a combined format).
- 🔘 **Size Scaling**: Adjust bubble size based on the number of login attempts.
- 🧭 **Legend & Opacity**: Tweak how the legend is displayed and the transparency level.

---

### 🔎 KQL Query Used

This Kusto Query pulls logon failure data from the `DeviceLogonEvents` table and enriches IPs with geolocation data via a custom GeoIP watchlist.

```kusto
let GeoIPDB_FULL = _GetWatchlist("geoip");
DeviceLogonEvents
| where ActionType == "LogonFailed"
| order by TimeGenerated desc
| evaluate ipv4_lookup(GeoIPDB_FULL, RemoteIP, network)
| summarize 
    LoginAttempts = count() 
    by 
    RemoteIP, 
    City = cityname, 
    Country = countryname, 
    friendly_location = strcat(cityname, " (", countryname, ")"), 
    Latitude = latitude, 
}
