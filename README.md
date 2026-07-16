# ShipXY-API
Try to query ship positions via the ShipXY API, cross-check with work logs to improve work efficiency.
## 1 Basic Logic
### 1.1 ShipXY-API
ShipXY refers to Chuanxun Network. ShipXY-API is a tool project built based on the open APIs of ShipXY. Its core function is to call official interfaces to batch query real-time vessel position information, cross-check the acquired data against daily work logs, streamline manual vessel lookup procedures, and boost the efficiency of shipping ledger verification and vessel dynamic tracking.
**Login and corresponding access permissions are required for usage.**

### 1.2 Pac Report Document Logic
We need to check the position of every vessel listed in the Excel file named **pac report** on a daily basis one by one.
| REF   | DATE  | PAC NO | CMO    | VESSELS ENG 船名 (英文) | PAC EMAIL DATE | PAC EMAIL TIME | 船員 | 實際到港日期 | 實際到港時間 | 到港確認 | 實際離港日期 | 實際離港時間 | 離港確認 |
| ----- | ----- | ------ | ------ | ---------------------- | -------------- | -------------- | ---- | ------------ | ------------ | -------- | ------------ | ------------ | -------- |
| 07093 |       | 21004  | 60825  | ZHEN DONG 813          | 11-07          | 1000           | 5    | 12-07        | 0527         | 050      |              |              |          |
| 07105 |       | 21119  | 58075  | ZHI LI 818             | 11-07          | 1600           | 3    | 11-07        | 1935         | 266      |              |              |          |
| 07111 |       | 21156  | 65064  | FENG NIAN 528          | 12-07          | 1000           | 6    | 12-07        | 0948         | 102      |              |              |          |
| 07114 |       | 21191  | 112721 | YUE HANG 113           | 12-07          | 1100           | 10   | 12-07        | 2034         | 254      |              |              |          |
| 07118 |       | 21226  | 89422  | ZHI LI 828             | 13-07          | 1000           | 3    | 13-07        | 2232         | 252      |              |              |          |
| 07122 |       | 21312  | 64883  | DE TONG 268            | 14-07          | 1100           | 6    | 14-07        | 1506         | 210      |              |              |          |
| 07123 |       | 21319  | 74323  | FENG NIAN 95           | 14-07          | 1100           | 6    | 14-07        | 1214         | 169      |              |              |          |
| 07124 |       | 21320  | 60939  | SHENG PING 33          | 14-07          | 1100           | 6    | 14-07        | 0933         | 118      |              |              |          |
| 07125 |       | 21323  | 74322  | FENG NIAN 96           | 14-07          | 1100           | 6    | 14-07        | 1506         | 211      |              |              |          |
| 07126 |       | 21329  | 60983  | FENG NIAN 88           | 14-07          | 1100           | 6    | 14-07        | 0933         | 117      |              |              |          |
| 07131 |       | 21414  | 111726 | SHUN YI 666            | 14-07          | 1600           | 6    | 15-07        | 1016         | 127      |              |              |          |
| 07132 | 14-07 | 21441  | 95547  | HUI JIN QIAO 292       | 15-07          | 1000           | 11   |              |              |          |              |              |          |
| 07133 |       | 21442  | 61915  | ZHEN DONG 588          | 15-07          | 1000           | 5    | 15-07        | 1016         | 126      |              |              |          |
| 07134 |       | 21447  | 68561  | CHENG XIANG 588        | 15-07          | 1000           | 7    | 15-07        | 0638         | 067      |              |              |          |
| 07135 |       | 21449  | 62616  | CHENG XIANG 598        | 15-07          | 1000           | 6    | 15-07        | 0628         | 066      |              |              |          |

Column G, titled **VESSELS ENG**, contains the English name of each ship.
In the adjacent column for actual arrival date: if the cell is blank, the vessel has not arrived in Hong Kong; if the cell is filled with content, the vessel has already arrived in Hong Kong.
The column for actual departure date follows the same judging rule.

Based on the data in the PAC report spreadsheet, the judging logic is defined as below:
1. When the Actual Arrival Date is empty → The vessel has not reached Hong Kong, and its position is outside Hong Kong harbour limits.
2. When Actual Arrival Date is non-empty but Actual Departure Date is empty → The vessel stays within Hong Kong waters and inside the harbour boundary.
3. When both Actual Arrival Date and Actual Departure Date are filled → The vessel has left Hong Kong waters and is outside harbour limits.

# 2 Traditional practice
<img width="1545" height="784" alt="image" src="https://github.com/user-attachments/assets/6d3618db-09e9-4d87-916d-a38d0c773e86" />
The dashed line in the picture is the boundary of Hong Kong waters. We need to go to the top left of the Ship News website and search each ship to find its location, then judge whether the ship's position is abnormal based on the basic logic described earlier.
In the traditional approach, the whole process has to rely on manually comparing each item one by one, which is really time-consuming.

# 3 ShipXY-API Detailed Interface Introduction
## 3.1 API Documentation
Here's the official link to Boat News： https://www.shipxy.com/  
The official Chain API introduction page of Chuanxun is： https://my.feishu.cn/wiki/GxF2w6cZHisQiEkBRatcoIqlnfc  
sdk https://my.feishu.cn/wiki/FGUAwCMVGiHYZQkHTv6cA7ZUnrf  

We can get the following information
| Interface Name | Request URL | Request Method |
| :--- | :--- | :--- |
| Get Single Ship Position | `https://api.shipxy.com/apicall/v3/GetSingleShip` | GET |
| Get Multiple Ships Position | `https://api.shipxy.com/apicall/v3/GetManyShip` | GET |
| Get Fleet Ships Position | `https://api.shipxy.com/apicall/v3/GetFleetShip` | GET |

Request Call Example  
Get Single Ship Position  
https://api.shipxy.com/apicall/v3/GetSingleShip?key=1F6D701272402D1E7D8D316CCE519123&mmsi=413961925  
Get Multiple Ships Position  
https://api.shipxy.com/apicall/v3/GetManyShip?key=1F6D701272402D1E7D8D316CCE519123&mmsis=413961925,477232800,477172700  
Get Fleet Ships Position  
https://api.shipxy.com/apicall/v3/GetFleetShip?key=1F6D701272402D1E7D8D316CCE519123&fleet_id=3e42a3db-6cdf-4c62-9473-8ad00348d646  

Parameter：Get Single Ship Position
| Attribute | Name | Type | Required | Description |
| :--- | :--- | :--- | :--- | :--- |
| `key` | Auth Code | string | Yes | ShipXY auth code |
| `mmsi` | Vessel MMSI | uint32 | Yes | 9-digit MMSI number |

Parameter：Get Multiple Ships Position
| Attribute | Name | Type | Required | Description |
| :--- | :--- | :--- | :--- | :--- |
| `key` | Authorization Code | string | Yes | ShipXY authorization code to verify service access. |
| `mmsis` | Vessel MMSI Numbers | uint32 | Yes | MMSI numbers separated by commas. Maximum 100 vessels per request. Note: Use `mmsis` instead of `mmsi`. Invalid MMSI values will not be charged; only valid ones are recorded in billing statistics. |

sample：  
```json
{
    'key': 'YOUR_API_KEY',
    'mmsis': '413961925,477232800,477172700'
}
```

API Response Parameters
| Attribute | Name | Type | Description |
| :--- | :--- | :--- | :--- |
| `mmsi` | Vessel MMSI | uint32 | 9-digit MMSI number |
| `imo` | IMO Number | uint32 | IMO identification number |
| `call_sign` | Call Sign | string | Vessel call sign |
| `ship_name` | Vessel Name | string | Vessel English name |
| `ship_cnname` | Chinese Name | string | Vessel Chinese name |
| `data_source` | Data Source | byte | 0: AIS Shore/Ship-based; 1: Satellite-based |
| `ship_type` | Ship Type | int | Ship type code (refer to appendix) |
| `length` | Length | float | Length in meters (0-1022) |
| `width` | Width | float | Width in meters |
| `left` | A-dim (Left) | float | Distance from bow in meters |
| `trail` | D-dim (Trail) | float | Distance from stern in meters |
| `draught` | Draught | float | Draught depth in meters |
| `dest` | Destination | string | Standardized destination port name (e.g., NINGBO,CN) |
| `destcode` | Destination Code | string | Destination port code (e.g., CNNBO) |
| `eta` | ETA (Local) | string | Estimated Time of Arrival: "YYYY-MM-DD HH:MM:SS" (Beijing Time) |
| `eta_utc` | ETA (UTC) | int | ETA as a Unix timestamp |
| `navistat` | Navigational Status | int | 0: Navigational status. -1: Invalid data |
| `lat` | Latitude | double | WGS84 latitude |
| `lng` | Longitude | double | WGS84 longitude |
| `sog` | Speed Over Ground | float | Speed in knots. -1: Invalid data |
| `cog` | Course Over Ground | float | Course in degrees. -1: Invalid data |
| `hdg` | True Heading | float | Heading in degrees. 511: Invalid data |
| `rot` | Rate of Turn | float | Rate of turn in degrees/minute |
| `last_time` | Last Update (Local)| string | Last AIS update time: "YYYY-MM-DD HH:MM:SS" (Beijing Time) |
| `last_time_utc` | Last Update (UTC) | int | Last update as a Unix timestamp |

```json
sample：  
{
  "status": 0,
  "msg": "",
  "data": {
    "mmsi": 413961925,
    "imo": 0,
    "call_sign": "P",
    "ship_name": "WANHONGYUAN369",
    "ship_cnname": "皖鸿远369",
    "data_source": 0,
    "ship_type": 70,
    "length": 68,
    "width": 13,
    "left": 8,
    "trail": 18,
    "draught": 4.8,
    "dest": "TAIZHOU,CN",
    "destcode": "CNTZO",
    "eta": "2025-03-31 02:09:00",
    "navistat": 0,
    "lat": 32.192517,
    "lng": 119.628093,
    "sog": 6.2,
    "cog": 80.8,
    "hdg": 511,
    "rot": 0,
    "last_time": "2025-04-28 16:05:48",
    "last_time_utc": 1745827548
  }
}
```

## 4 Core Code：Read the ship names from Excel, find the MMSI mapping, and send it to the Ship Information API  
```python

import os
import requests
import pandas as pd
import json

# ==================== Configuration ====================
# Please enter your ShipXY API Key here
API_KEY = "API_KEY_HERE"

# Vessel Name to MMSI mapping dictionary
# Batch queries in ShipXY API require MMSI (9-digit number)
VESSEL_MMSI_MAP = {
    "ZHEN DONG 813": 413961925,
    "ZHI LI 818": 477232800,
    "FENG NIAN 528": 477172700,
    "YUE HANG 113": 413000113,
    "ZHI LI 828": 413000828,
    "DE TONG 268": 413000268,
    "FENG NIAN 95": 413000095,
    "SHENG PING 33": 413000033,
    "FENG NIAN 96": 413000096,
    "FENG NIAN 88": 413000088,
    "SHUN YI 666": 413000666,
    "HUI JIN QIAO 292": 413000292,
    "ZHEN DONG 588": 413000588,
    "CHENG XIANG 588": 413000589,
    "CHENG XIANG 598": 413000598,
}
# =======================================================

def get_vessel_positions(excel_path):
    """
    Reads vessel names from the Excel file, maps them to MMSI, 
    and requests position data from ShipXY API.
    """
    if not os.path.exists(excel_path):
        print(f"Error: Excel file '{excel_path}' not found.")
        return

    print(f"Reading Excel file: {excel_path}...")
    
    try:
        # Read column G (index 6). Skip header (first row).
        df = pd.read_excel(excel_path, usecols="G", skiprows=1, header=None)
    except Exception as e:
        print(f"Failed to read Excel: {e}")
        return

    # Extract vessel names and filter out empty values
    raw_vessel_names = df[6].dropna().tolist()
    vessel_names = [str(name).strip() for name in raw_vessel_names if str(name).strip()]
    
    if not vessel_names:
        print("No valid vessel names found in column G.")
        return
        
    print(f"Successfully read {len(vessel_names)} vessel names.")

    # Map vessel names to MMSI
    mmsi_list = []
    unmapped_vessels = []
    
    for name in vessel_names:
        mmsi = VESSEL_MMSI_MAP.get(name) or VESSEL_MMSI_MAP.get(name.upper())
        if mmsi:
            mmsi_list.append(str(mmsi))
        else:
            unmapped_vessels.append(name)

    if unmapped_vessels:
        print(f"\n⚠️ Warning: {len(unmapped_vessels)} vessels could not be mapped:")
        print(unmapped_vessels)

    if not mmsi_list:
        print("\n❌ Error: No vessel names successfully mapped to MMSI.")
        return

    print(f"\nSuccessfully matched {len(mmsi_list)} MMSI numbers.")

    # API batch limit is 100 per request
    chunk_size = 100
    api_url = "https://api.shipxy.com/apicall/v3/GetManyShip"
    
    all_responses = []

    for i in range(0, len(mmsi_list), chunk_size):
        chunk = mmsi_list[i : i + chunk_size]
        params = {
            "key": API_KEY,
            "mmsis": ",".join(chunk)
        }
        
        print(f"\nSending request batch {i//chunk_size + 1}, containing {len(chunk)} vessels...")
        
        try:
            response = requests.get(api_url, params=params, timeout=15)
            response.raise_for_status()
            result = response.json()
            all_responses.append(result)
            print("Request successful.")
        except requests.exceptions.RequestException as e:
            print(f"Request failed: {e}")
            
    return all_responses

if __name__ == "__main__":
    EXCEL_FILE_PATH = "pac_report.xlsx" 
    api_data = get_vessel_positions(EXCEL_FILE_PATH)
    
    if api_data:
        print("\nFirst batch API response preview:")
        print(json.dumps(api_data[0], indent=4, ensure_ascii=False)[:1000])
```

Key information of the structure sent to the API

```json
{
    "url": "https://api.shipxy.com/apicall/v3/GetManyShip",
    "method": "GET",
    "headers": {
        "User-Agent": "python-requests/2.x.x",
        "Accept": "*/*"
    },
    "params": {
        "key": "YOUR_API_KEY_HERE",
        "mmsis": "413961925,477232800,477172700,413000113"
    }
}
```

Key information of the structure returned by the API

```json
{
    "status": 0,
    "msg": "Success",
    "data": [
        {
            "mmsi": 413961925,
            "imo": 0,
            "call_sign": "XY01",
            "ship_name": "ZHEN DONG 813",
            "ship_cnname": "振东813",
            "lat": 22.2854,
            "lng": 114.1577,
            "heading": 180,
            "course": 180.5,
            "speed": 10.2,
            "status": 0,
            "last_time": "2026-07-16 12:00:00"
        },
        {
            "mmsi": 477232800,
            "imo": 9123456,
            "call_sign": "XY02",
            "ship_name": "ZHI LI 818",
            "ship_cnname": "智利818",
            "lat": 22.3120,
            "lng": 114.1245,
            "heading": 90,
            "course": 90.0,
            "speed": 0.1,
            "status": 1,
            "last_time": "2026-07-16 12:05:00"
        }
    ]
}
```

## 5 Core Code：Receive and parse latitude and longitude information from the Ship Info API 
```Python
import json

def parse_vessel_positions(api_response_list):
    """
    Parses the API response to extract vessel names, latitudes, and longitudes.
    
    :param api_response_list: List of JSON responses (or dicts) returned by the ShipXY API.
    :return: A list of dictionaries containing extracted vessel details.
    """
    extracted_data = []

    # Iterate through each batch response
    for response in api_response_list:
        # Check if response is a string, and parse it to dict if necessary
        if isinstance(response, str):
            try:
                response = json.loads(response)
            except json.JSONDecodeError as e:
                print(f"Error decoding JSON string: {e}")
                continue

        # Check if response is valid and contains data
        # Note: Adjust 'data' or response structure key depending on the actual API payload envelope
        vessels = response if isinstance(response, list) else response.get("data", [])
        
        if not isinstance(vessels, list):
            # If the data itself is a single vessel dictionary inside a dict
            vessels = [vessels]

        for vessel in vessels:
            # Safely extract values using .get() to avoid KeyError
            mmsi = vessel.get("mmsi")
            ship_name = vessel.get("ship_name")      # English name
            ship_cnname = vessel.get("ship_cnname")  # Chinese name
            lat = vessel.get("lat")                  # Latitude (WGS84)
            lng = vessel.get("lng")                  # Longitude (WGS84)

            # Store the structured data
            extracted_data.append({
                "mmsi": mmsi,
                "ship_name": ship_name,
                "ship_cnname": ship_cnname,
                "latitude": lat,
                "longitude": lng
            })

    return extracted_data


# ==================== Verification & Mock Test ====================
if __name__ == "__main__":
    # Mock data representing a typical ShipXY API payload response
    mock_api_response = [
        {
            "mmsi": 413961925,
            "imo": 0,
            "call_sign": "XY01",
            "ship_name": "ZHEN DONG 813",
            "ship_cnname": "振东813",
            "lat": 22.2854,
            "lng": 114.1577,
            "sog": 10.2,
            "cog": 180.5,
            "last_time": "2026-07-16 12:00:00"
        },
        {
            "mmsi": 477232800,
            "imo": 9123456,
            "call_sign": "XY02",
            "ship_name": "ZHI LI 818",
            "ship_cnname": "智利818",
            "lat": 22.3120,
            "lng": 114.1245,
            "sog": 0.1,
            "cog": 90.0,
            "last_time": "2026-07-16 12:05:00"
        }
    ]

    # Wrap mock response in a list (simulating batched API returns)
    api_responses = [mock_api_response]

    # Parse data
    vessel_list = parse_vessel_positions(api_responses)

    # Print results
    print(f"Extracted {len(vessel_list)} vessel records:")
    print(json.dumps(vessel_list, indent=4, ensure_ascii=False))
```

Key information of the output structure：

```json
Extracted 2 vessel records:
[
    {
        "mmsi": 413961925,
        "ship_name": "ZHEN DONG 813",
        "ship_cnname": "振东813",
        "latitude": 22.2854,
        "longitude": 114.1577
    },
    {
        "mmsi": 477232800,
        "ship_name": "ZHI LI 818",
        "ship_cnname": "智利818",
        "latitude": 22.312,
        "longitude": 114.1245
    }
]
```

## 6 Core Code：Based on the latitude and longitude information returned from the Ship Info API, and combining it with the logic in the previous PAC report document, determine whether the ship's position is normal.

```Python
import os
import pandas as pd
from shapely.geometry import Point, Polygon
import json

# ==================== Configuration ====================
# Mapping dictionary: Vessel Name to MMSI
VESSEL_MMSI_MAP = {
    "ZHEN DONG 813": 413961925,
    "ZHI LI 818": 477232800,
    "FENG NIAN 528": 477172700,
    "YUE HANG 113": 413000113,
    "ZHI LI 828": 413000828,
    "DE TONG 268": 413000268,
    "FENG NIAN 95": 413000095,
    "SHENG PING 33": 413000033,
    "FENG NIAN 96": 413000096,
    "FENG NIAN 88": 413000088,
    "SHUN YI 666": 413000666,
    "HUI JIN QIAO 292": 413000292,
    "ZHEN DONG 588": 413000588,
    "CHENG XIANG 588": 413000589,
    "CHENG XIANG 598": 413000598,
}

# Define Hong Kong waters boundary polygon (longitude, latitude)
HK_WATERS_POLYGON = Polygon([
    (113.833, 22.450), (113.916, 22.550), (114.216, 22.550),
    (114.300, 22.550), (114.433, 22.550), (114.500, 22.416),
    (114.500, 22.200), (114.166, 22.150), (113.833, 22.150),
    (113.833, 22.350)
])
# =======================================================

def check_if_in_hk_waters(lat, lng):
    """
    Check if a coordinate point is within Hong Kong waters using Shapely.
    """
    if pd.isna(lat) or pd.isna(lng):
        return "Unknown"
    point = Point(lng, lat)  # Shapely expects (longitude, latitude)
    return "In-Port" if HK_WATERS_POLYGON.contains(point) else "Out-Port"


def extract_expected_statuses(excel_path):
    """
    Extract vessel names from Excel and determine the expected location
    based on arrival and departure dates.
    """
    if not os.path.exists(excel_path):
        print(f"Error: Could not find Excel file '{excel_path}'")
        return {}

    df = pd.read_excel(excel_path)
    df.columns = [str(col).strip() for col in df.columns]

    # Column identification
    vessel_col = "VESSELS ENG 船名 (英文)"
    arrival_col = "實際到港日期"
    departure_col = "實際離港日期"

    expected_positions = {}

    for _, row in df.iterrows():
        vessel_name = str(row[vessel_col]).strip() if pd.notna(row[vessel_col]) else None
        if not vessel_name:
            continue

        arrival_date = row[arrival_col]
        departure_date = row[departure_col]

        # Logic for expected position:
        # 1. Arrival Date is empty -> Should be Out-Port
        if pd.isna(arrival_date) or str(arrival_date).strip() == "":
            expected = "Out-Port"
        # 2. Arrival filled, Departure empty -> Should be In-Port
        elif pd.notna(arrival_date) and (pd.isna(departure_date) or str(departure_date).strip() == ""):
            expected = "In-Port"
        # 3. Both filled -> Should be Out-Port (Departed)
        else:
            expected = "Out-Port"

        expected_positions[vessel_name] = expected

    return expected_positions


def verify_vessel_positions(excel_path, api_response_list):
    """
    Core verification logic: Cross-reference expected location from Excel
    with actual location from API.
    """
    expected_map = extract_expected_statuses(excel_path)
    
    actual_positions = {}
    for response in api_response_list:
        vessels = response if isinstance(response, list) else response.get("data", [])
        for vessel in vessels:
            ship_name = vessel.get("ship_name", "").strip()
            lat = vessel.get("lat")
            lng = vessel.get("lng")
            
            actual_loc = check_if_in_hk_waters(lat, lng)
            actual_positions[ship_name] = {
                "mmsi": vessel.get("mmsi"),
                "actual_location": actual_loc
            }

    final_results = []

    for vessel_name, expected_loc in expected_map.items():
        mmsi = VESSEL_MMSI_MAP.get(vessel_name) or VESSEL_MMSI_MAP.get(vessel_name.upper(), "N/A")
        
        status = "Unknown (No API Data)"
        actual_loc = "No Signal"

        if vessel_name in actual_positions:
            actual_info = actual_positions[vessel_name]
            actual_loc = actual_info["actual_location"]
            
            if actual_loc == "Unknown":
                status = "Unknown (Invalid Position)"
            elif expected_loc == actual_loc:
                status = "Normal"
            else:
                status = "Abnormal"

        final_results.append({
            "Vessel Name": vessel_name,
            "MMSI": mmsi,
            "Expected": expected_loc,
            "Actual": actual_loc,
            "Status": status
        })

    return final_results

# ==================== Verification ====================
if __name__ == "__main__":
    mock_api_response = [
        {"mmsi": 413961925, "ship_name": "ZHEN DONG 813", "lat": 22.2854, "lng": 114.1577},
        {"mmsi": 413000292, "ship_name": "HUI JIN QIAO 292", "lat": 22.3120, "lng": 114.1245}
    ]

    EXCEL_PATH = "pac_report.xlsx"
    results = verify_vessel_positions(EXCEL_PATH, [mock_api_response])

    print("\n" + "="*20 + " Final Verification Results " + "="*20)
    print(json.dumps(results, indent=4, ensure_ascii=False))
```

Key information of the output structure
```json
[
    {
        "Vessel Name": "ZHEN DONG 813",
        "MMSI": 413961925,
        "Expected": "In-Port",
        "Actual": "In-Port",
        "Status": "Normal"
    },
    {
        "Vessel Name": "HUI JIN QIAO 292",
        "MMSI": 413000292,
        "Expected": "Out-Port",
        "Actual": "In-Port",
        "Status": "Abnormal"
    },
    {
        "Vessel Name": "DE TONG 268",
        "MMSI": 413000268,
        "Expected": "In-Port",
        "Actual": "In-Port",
        "Status": "Normal"
    }
]
```
