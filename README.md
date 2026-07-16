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
