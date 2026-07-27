---
title: "Event API"
toc_label: "Event"  
---

## Log

```protobuf
message EventLog {
  uint32 ID;
  uint32 timestamp;
  uint32 deviceID;
  string userID;
  uint32 entityID;
  uint32 eventCode;
  uint32 subCode;
  tna.Key TNAKey;
  bool hasImage;
  bool changedOnDevice;
  uint32 temperature;
  bytes cardData;
  DetectInputInfo inputInfo;
  AlarmZoneInfo alarmZoneInfo;
  InterlockZoneInfo interlockZoneInfo;
}
```
{: #EventLog}

ID
: 4 byte identifier of the log record. Each device manages a monotonic increasing counter for this identifier. You can use this value to specify the starting position when reading logs from devices.

timestamp
: In Unix time format. The number of seconds elapsed since January 1, 1970.

userID/entityID
: For user related events such as __EVENT_VERIFY_SUCCESS__ or __EVENT_USER_ENROLL_SUCCESS__, the __userID__ will be set. For other types of events, the __entity_ID__ could be either a door ID or zone ID. 

eventCode
: 16 bit code identifying the event type.

  | Category | Code | Value | Description |
  | -------- | ---- | ------| ----------- |
  | Auth | BS2_EVENT_VERIFY_SUCCESS | 0x1000 | 1:1 authentication success |
  || BS2_EVENT_VERIFY_FAIL | 0x1100 | 1:1 authentication failure |
  || BS2_EVENT_VERIFY_DURESS | 0x1200 | 1:1 authentication success under duress |
  || BS2_EVENT_IDENTIFY_SUCCESS | 0x1300 | 1:N authentication success |
  || BS2_EVENT_IDENTIFY_FAIL | 0x1400 | 1:N authentication failure |
  || BS2_EVENT_IDENTIFY_DURESS | 0x1500 | 1:N authentication success under duress |
  || BS2_EVENT_DUAL_AUTH_SUCCESS | 0x1600 | dual authentication success |
  || BS2_EVENT_DUAL_AUTH_FAIL | 0x1700 | dual authentication failure |
  || BS2_EVENT_AUTH_FAILED | 0x1800 | unregistered credential |
  || BS2_EVENT_ACCESS_DENIED | 0x1900 | user without access privileges or violation of zone rules |
  || BS2_EVENT_FAKE_FINGER_DETECTED | 0x1A00 | fake finger detected |
  || BS2_EVENT_BYPASS_SUCCESS | 0x1B00 | Access granted after checking mask or temperature |
  || BS2_EVENT_BYPASS_FAIL | 0x1C00 | Access denied after checking mask or temperature |
  || BS2_EVENT_ABNORMAL_TEMPERATURE_DETECTED | 0x1D00 | Abnormal temperature detected |
  || BS2_EVENT_UNMASKED_FACE_DETECTED | 0x1E00 | No mask detected |
  | User | BS2_EVENT_USER_ENROLL_SUCCESS | 0x2000 | user enrollment success |
  || BS2_EVENT_USER_ENROLL_FAIL | 0x2100 | user enrollment failure |
  || BS2_EVENT_USER_UPDATE_SUCCESS | 0x2200 | user update success |
  || BS2_EVENT_USER_UPDATE_FAIL | 0x2300 | user update failure |
  || BS2_EVENT_USER_DELETE_SUCCESS | 0x2400 | user delete success |
  || BS2_EVENT_USER_DELETE_FAIL | 0x2500 | user delete failure |
  || BS2_EVENT_USER_DELETE_ALL_SUCCESS | 0x2600 | delete all user success |
  || BS2_EVENT_USER_ISSUE_AOC_SUCCESS | 0x2700 | issuance of an AOC card success |
  || BS2_EVENT_USER_DUPLICATE_CREDENTIAL | 0x2800 | duplicate credential |
  || BS2_EVENT_USER_UPDATE_PARTIAL_SUCCESS | 0x2900 | User partial update success |
  || BS2_EVENT_USER_UPDATE_PARTIAL_FAIL | 0x2A00 | User partial update failure |
  || BS2_EVENT_USER_RELOADED | 0x2B00 | User reloaded |
  | Device | BS2_EVENT_DEVICE_SYSTEM_RESET | 0x3000 | system reset |
  || BS2_EVENT_DEVICE_SYSTEM_ERROR_OPENGL | 0x3050 | OpenGL error |
  || BS2_EVENT_DEVICE_SYSTEM_STARTED | 0x3100 | system started |
  || BS2_EVENT_DEVICE_TIME_SET | 0x3200 | system time set |
  || BS2_EVENT_DEVICE_TIMEZONE_SET | 0x3201 | timezone changed |
  || BS2_EVENT_DEVICE_DST_SET | 0x3202 | DST changed |
  || BS2_EVENT_DEVICE_LINK_CONNECTED | 0x3300 | LAN cable connected |
  || BS2_EVENT_DEVICE_LINK_DISCONNECTED | 0x3400 | LAN cable disconnected |
  || BS2_EVENT_DEVICE_DHCP_SUCCESS | 0x3500 | IP address acquired by DHCP |
  || BS2_EVENT_DEVICE_ADMIN_MENU | 0x3600 | enter administrator menu |
  || BS2_EVENT_DEVICE_ADMIN_LOGIN_FAIL | 0x3601 | Administrator login failure |
  || BS2_EVENT_DEVICE_ADMIN_LOGIN_FAIL_NOCREDENTIAL | 0x3602 | Administrator login failure - no credential |
  || BS2_EVENT_DEVICE_UI_LOCKED | 0x3700 | device locked |
  || BS2_EVENT_DEVICE_UI_UNLOCKED | 0x3800 | device unlocked |
  || BS2_EVENT_DEVICE_TCP_CONNECTED | 0x3B00 | TCP connected |
  || BS2_EVENT_DEVICE_TCP_DISCONNECTED | 0x3C00 | TCP disconnected |
  || BS2_EVENT_DEVICE_RTSP_CONNECTED | 0x3B10 | RTSP connected |
  || BS2_EVENT_DEVICE_RTSP_DISCONNECTED | 0x3C10 | RTSP disconnected |
  || BS2_EVENT_DEVICE_RS485_CONNECTED | 0x3D00 | RS485 connected |
  || BS2_EVENT_DEVICE_RS485_DISCONNECTED | 0x3E00 | RS485 disconnected |
  || BS2_EVENT_DEVICE_IO_DETECTED | 0x3F00 | IO signal detected |
  || BS2_EVENT_DEVICE_TAMPER_ON | 0x4000 | tamper SW is on |
  || BS2_EVENT_DEVICE_TAMPER_OFF | 0x4100 | tamper SW is off |
  || BS2_EVENT_DEVICE_EVENT_LOG_CLEARED | 0x4200 | log records cleared |
  || BS2_EVENT_DEVICE_FIRMWARE_UPGRADED | 0x4300 | firmware upgraded |
  || BS2_EVENT_DEVICE_RESOURCE_UPGRADED | 0x4400 | resource upgraded |
  || BS2_EVENT_DEVICE_CONFIG_RESET | 0x4500 | system configurations initialized (including network) |
  || BS2_EVENT_DEVICE_DATABASE_RESET | 0x4501 | database initialized |
  || BS2_EVENT_DEVICE_FACTORY_RESET | 0x4502 | factory reset |
  || BS2_EVENT_DEVICE_CONFIG_RESET_EX | 0x4503 | system configurations initialized (excluding network) |
  || BS2_EVENT_DEVICE_FACTORY_RESET_WITHOUT_ETHERNET | 0x4504 | Factory reset - excluding network |
  || BS2_EVENT_SUPERVISED_INPUT_SHORT | 0x4600 | short circuit of a supervised input detected |
  || BS2_EVENT_SUPERVISED_INPUT_OPEN | 0x4700 | disconnection of a supervised input detected |
  || BS2_EVENT_DEVICE_AC_FAIL | 0x4800 | Access control failure |
  || BS2_EVENT_DEVICE_AC_SUCCESS | 0x4900 | Access control success |
  || BS2_EVENT_EXIT_BUTTON | 0x4A00 | Exit button |
  || BS2_EVENT_SIMULATED_EXIT_BUTTON | 0x4A01 | Simulated exit button |
  || BS2_EVENT_OPERATOR_OPEN | 0x4B00 | Operator open |
  || BS2_EVENT_VOIP_OPEN | 0x4C00 | Interphone open |
  || BS2_EVENT_LICENSE_ENABLE_SUCCESS | 0x4D00 | Device license enable success |
  || BS2_EVENT_LICENSE_ENABLE_FAIL | 0x4D01 | Device license enable failure |
  || BS2_EVENT_LICENSE_DISABLE_SUCCESS | 0x4D02 | Device license disable success |
  || BS2_EVENT_LICENSE_DISABLE_FAIL | 0x4D03 | Device license disable failure |
  || BS2_EVENT_LICENSE_EXPIRED | 0x4D04 | Device license expired |
  || BS2_EVENT_RELAY_ACTIVATE_REQUESTED_BY_OPERATOR | 0x4F10 | Relay activate requested by operator |
  || BS2_EVENT_RELAY_DEACTIVATE_REQUESTED_BY_OPERATOR | 0x4F20 | Relay deactivate requested by operator |
  | Door | BS2_EVENT_DOOR_UNLOCKED | 0x5000 | door unlocked |
  || BS2_EVENT_DOOR_LOCKED | 0x5100 | door locked |
  || BS2_EVENT_DOOR_OPENED | 0x5200 | door open detected by sensor |
  || BS2_EVENT_DOOR_CLOSED | 0x5300 | door closed detected by sensor |
  || BS2_EVENT_DOOR_FORCED_OPEN | 0x5400 | door forced open |
  || BS2_EVENT_DOOR_HELD_OPEN | 0x5500 | door held open too long |
  || BS2_EVENT_DOOR_FORCED_OPEN_ALARM | 0x5600 | forced open alarm |
  || BS2_EVENT_DOOR_OPEN_LOCKOVERRIDE_ALARM | 0x5601 | Door open (lock override) |
  || BS2_EVENT_DOOR_FORCED_OPEN_ALARM_CLEAR | 0x5700 | forced open alarm cleared |
  || BS2_EVENT_DOOR_OPEN_LOCKOVERRIDE_ALARM_CLEAR | 0x5702 | Door open (lock override) cleared |
  || BS2_EVENT_DOOR_HELD_OPEN_ALARM | 0x5800 | held open alarm |
  || BS2_EVENT_DOOR_HELD_OPEN_ALARM_CLEAR | 0x5900 | held open alarm cleared |
  || BS2_EVENT_DOOR_APB_ALARM | 0x5A00 | anti-passback alarm on a door |
  || BS2_EVENT_DOOR_APB_ALARM_CLEAR | 0x5B00 | anti-passback alarm on a door cleared |
  || BS2_EVENT_DOOR_RELEASE | 0x5C00 | door status reset |
  || BS2_EVENT_DOOR_LOCK | 0x5D00 | lock door |
  || BS2_EVENT_DOOR_LOCK_REQUEST_BY_SCHEDULE | 0x5D01 | Door lock requested by schedule |
  || BS2_EVENT_DOOR_LOCK_REQUEST_BY_FIRE_ALARM | 0x5D02 | Door lock requested by fire alarm |
  || BS2_EVENT_DOOR_LOCK_REQUEST_BY_OPERATOR | 0x5D04 | Door lock requested by operator |
  || BS2_EVENT_DOOR_UNLOCK | 0x5E00 | unlock door |
  || BS2_EVENT_DOOR_UNLOCK_REQUEST_BY_SCHEDULE | 0x5E01 | Door unlock requested by schedule |
  || BS2_EVENT_DOOR_UNLOCK_REQUEST_BY_FIRE_ALARM | 0x5E02 | Door unlock requested by emergency |
  || BS2_EVENT_DOOR_UNLOCK_REQUEST_BY_OPERATOR | 0x5E04 | Door unlock requested by operator |
  || BS2_EVENT_DOOR_UNLOCK_REQUEST_BY_LOCKOVERRIDE | 0x5E05 | Door unlock requested by lock override |
  || BS2_EVENT_DOOR_SEND_UNLOCK_TIMER | 0x5F00 | Send unlock by timer |
  || BS2_EVENT_DOOR_SEND_UNLOCK | 0x5F01 | Send unlock command |
  || BS2_EVENT_DOOR_SEND_LOCK | 0x5F02 | Send lock command |
  || BS2_EVENT_DOOR_SEND_RELEASE | 0x5F03 | Send door release command |
  || BS2_EVENT_DOOR_FIRE_BUTTON_INPUT | 0x5F04 | Fire button input detected |
  || BS2_EVENT_DOOR_FIRE_ALARM | 0x5F05 | Lock override alarm activated |
  || BS2_EVENT_DOOR_FIRE_ALARM_CLEAR | 0x5F06 | Lock override alarm cleared |
  || BS2_EVENT_DOOR_NORMALIZED | 0x5F07 | Door status normalized |
  | Zone | BS2_EVENT_ZONE_APB_VIOLATION | 0x6000 | APB zone violated |
  || BS2_EVENT_ZONE_APB_ALARM | 0x6100 | APB zone alarm |
  || BS2_EVENT_ZONE_APB_ALARM_CLEAR | 0x6200 | APB zone alarm cleared |
  || BS2_EVENT_ZONE_TIMED_APB_VIOLATION | 0x6300 | timed APB zone violated |
  || BS2_EVENT_ZONE_TIMED_APB_ALARM | 0x6400 | timed APB zone alarm |
  || BS2_EVENT_ZONE_TIMED_APB_ALARM_CLEAR | 0x6500 | timed APB zone alarm cleared |
  || BS2_EVENT_ZONE_FIRE_ALARM_INPUT | 0x6600 | fire alarm input detected |
  || BS2_EVENT_ZONE_FIRE_ALARM | 0x6700 | fire alarm |
  || BS2_EVENT_ZONE_FIRE_ALARM_CLEAR | 0x6800 | fire alarm cleared |
  || BS2_EVENT_ZONE_SCHEDULED_LOCK_VIOLATION | 0x6900 | scheduled lock zone violated |
  || BS2_EVENT_ZONE_SCHEDULED_LOCK_START | 0x6A00 | start of lock schedule |
  || BS2_EVENT_ZONE_SCHEDULED_LOCK_END | 0x6B00 | end of lock schedule |
  || BS2_EVENT_ZONE_SCHEDULED_UNLOCK_START | 0x6C00 | start of unlock schedule |
  || BS2_EVENT_ZONE_SCHEDULED_UNLOCK_END | 0x6D00 | end of unlock schedule |
  || BS2_EVENT_ZONE_SCHEDULED_LOCK_ALARM | 0x6E00 | scheduled lock zone alarm |
  || BS2_EVENT_ZONE_SCHEDULED_LOCK_ALARM_CLEAR | 0x6F00 | scheduled lock zone alarm cleared |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_VIOLATION | 0x9000 | intrusion zone violated |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_ARM_GRANTED | 0x9100 | arming intrusion zone |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_ARM_SUCCESS | 0x9200 | intrusion zone armed |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_ARM_FAIL | 0x9300 | arming intrusion zone failure |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_DISARM_GRANTED | 0x9400 | disarming intrusion zone |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_DISARM_SUCCESS | 0x9500 | intrusion zone disarmed |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_DISARM_FAIL | 0x9600 | disarming intrusion zone failure |
  || BS2_EVENT_ZONE_INTRUSION_ALARM | 0x9800 | intrusion alarm |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_CLEAR | 0x9900 | intrusion alarm  cleared |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_ARM_DENIED | 0x9A00 | arming intrusion zone denied |
  || BS2_EVENT_ZONE_INTRUSION_ALARM_DISARM_DENIED | 0x9B00 | disarming intrusion zone denied |
  || BS2_EVENT_ZONE_INTERLOCK_VIOLATION | 0xA000 | Interlock zone violated |
  || BS2_EVENT_ZONE_INTERLOCK_ALARM | 0xA100 | Interlock alarm |
  || BS2_EVENT_ZONE_INTERLOCK_ALARM_DOOR_OPEN_DENIED | 0xA200 | Interlock alarm(door open) |
  || BS2_EVENT_ZONE_INTERLOCK_ALARM_INDOOR_DENIED | 0xA300 | Interlock alarm(man in door) |
  || BS2_EVENT_ZONE_INTERLOCK_ALARM_CLEAR | 0xA400 | Interlock alarm cleared |
  || BS2_EVENT_ZONE_AUTH_LIMIT_VIOLATION | 0xA500 | Authentication limit zone violated |
  || BS2_EVENT_GLOBAL_AUTH_LIMIT_EXCUSED | 0xA600 | Global Authentication limit excused |
  || BS2_EVENT_ZONE_OCCUPANCY_LIMIT_VIOLATION | 0xA700 | Occupancy limit zone violated |
  || BS2_EVENT_GLOBAL_OCCUPANCY_LIMIT_EXCUSED | 0xA800 | Global Occupancy limit excused |
  || BS2_EVENT_ZONE_OCCUPANCY_LIMIT_ALARM | 0xA900 | Occupancy limit zone alarm |
  || BS2_EVENT_ZONE_OCCUPANCY_LIMIT_ALARM_CLEAR | 0xAA00 | Occupancy limit zone alarm cleared |
  || BS2_EVENT_ZONE_MUSTER_VIOLATION | 0xB000 | Muster zone violated |
  || BS2_EVENT_ZONE_MUSTER_ALARM | 0xB100 | Muster zone alarm |
  || BS2_EVENT_ZONE_MUSTER_ALARM_CLEAR | 0xB200 | Muster zone alarm cleared |
  || BS2_EVENT_ZONE_LIFT_LOCK_VIOLATION | 0xB500 | Lift lock zone violated |
  || BS2_EVENT_ZONE_LIFT_LOCK_START | 0xB600 | Start of lift lock schedule |
  || BS2_EVENT_ZONE_LIFT_LOCK_END | 0xB700 | End of lift lock schedule |
  || BS2_EVENT_ZONE_LIFT_UNLOCK_START | 0xB800 | Start of lift unlock schedule |
  || BS2_EVENT_ZONE_LIFT_UNLOCK_END | 0xB900 | End of lift unlock schedule |
  || BS2_EVENT_ZONE_LIFT_LOCK_ALARM | 0xBA00 | Lift lock zone alarm |
  || BS2_EVENT_ZONE_LIFT_LOCK_ALARM_CLEAR | 0xBB00 | Lift lock zone alarm cleared |
  || BS2_EVENT_DEVICE_USER_SYNC_TO_SERVER_FAIL | 0xC000 | User sync to server failure |
  || BS2_EVENT_BREAK_GLASS | 0xC100 | Break glass |
  || BS2_EVENT_MEMORY_FULL_MIGRATION | 0xC200 | Memory full migration |
  | Lift | BS2_EVENT_FLOOR_ACTIVATED | 0x7000 | floor activated |
  || BS2_EVENT_FLOOR_DEACTIVATED | 0x7100 | floor deactivated |
  || BS2_EVENT_FLOOR_RELEASE | 0x7200 | floor status reset |
  || BS2_EVENT_FLOOR_ACTIVATE | 0x7300 | activate floor |
  || BS2_EVENT_FLOOR_DEACTIVATE | 0x7400 | deactivate floor |
  || BS2_EVENT_LIFT_ALARM_INPUT | 0x7500 | lift alarm input detected |
  || BS2_EVENT_LIFT_ALARM | 0x7600 | lift alarm |
  || BS2_EVENT_LIFT_ALARM_CLEAR | 0x7700 | lift alarm cleared |
  || BS2_EVENT_ALL_FLOOR_ACTIVATED | 0x7800 | all floor activated |
  || BS2_EVENT_ALL_FLOOR_DEACTIVATED | 0x7900 | all floor deactivated |
  || BS2_EVENT_RELAY_ACTION_ON | 0xC300 | Relay action switch-on |
  || BS2_EVENT_RELAY_ACTION_OFF | 0xC400 | Relay action switch-off |
  || BS2_EVENT_RELAY_ACTION_KEEP | 0xC500 | Relay action keep signal |
  {: #EventCode}

subCode
: Some event types have an additional 8 bit code providing auxiliary information. For example, __BS2_EVENT_VERIFY_XXXX__ events have a sub code denoting the authentication mode. If the __eventCode__ is BS2_EVENT_VERIFY_SUCCESS and the __subCode__ is BS2_SUB_EVENT_VERIFY_CARD_FINGER, it means that the user authenticated Card + Fingerprint successfully.

  | Category | Code | Value | Description |
  | -------- | ---- | ------| ----------- |
  | Verify | BS2_SUB_EVENT_VERIFY_ID_PIN | 0x01 | ID + PIN |
  || BS2_SUB_EVENT_VERIFY_ID_FINGER | 0x02 | ID + fingerprint |
  || BS2_SUB_EVENT_VERIFY_ID_FINGER_PIN | 0x03 | ID + fingerprint + PIN |
  || BS2_SUB_EVENT_VERIFY_ID_FACE | 0x04 | ID + face |
  || BS2_SUB_EVENT_VERIFY_ID_FACE_PIN | 0x05 | ID + face + PIN |
  || BS2_SUB_EVENT_VERIFY_CARD | 0x06 | card only |
  || BS2_SUB_EVENT_VERIFY_CARD_PIN | 0x07 | card + PIN |
  || BS2_SUB_EVENT_VERIFY_CARD_FINGER | 0x08 | card + fingerprint |
  || BS2_SUB_EVENT_VERIFY_CARD_FINGER_PIN | 0x09 | card + fingerprint + PIN |
  || BS2_SUB_EVENT_VERIFY_CARD_FACE | 0x0A | card + face |
  || BS2_SUB_EVENT_VERIFY_CARD_FACE_PIN | 0x0B | card + face + PIN |
  || BS2_SUB_EVENT_VERIFY_AOC | 0x0C | AOC card |
  || BS2_SUB_EVENT_VERIFY_AOC_PIN | 0x0D | AOC card + PIN |
  || BS2_SUB_EVENT_VERIFY_AOC_FINGER | 0x0E | AOC card + fingerprint |
  || BS2_SUB_EVENT_VERIFY_AOC_FINGER_PIN | 0x0F | AOC card + fingerprint + PIN |
  || BS2_SUB_EVENT_VERIFY_CARD_FACE_FINGER | 0x10 | card + face + fingerprint |
  || BS2_SUB_EVENT_VERIFY_CARD_FINGER_FACE | 0x11 | card + fingerprint + face |
  || BS2_SUB_EVENT_VERIFY_ID_FACE_FINGER | 0x12 | ID + face + fingerprint |
  || BS2_SUB_EVENT_VERIFY_ID_FINGER_FACE | 0x13 | ID + fingerprint + face |
  || BS2_SUB_EVENT_VERIFY_MOBLIE_CARD | 0x16 | mobile |
  || BS2_SUB_EVENT_VERIFY_MOBILE_CARD_PIN | 0x17 | mobile + PIN |
  || BS2_SUB_EVENT_VERIFY_MOBILE_CARD_FINGER | 0x18 | mobile + finger |
  || BS2_SUB_EVENT_VERIFY_MOBILE_CARD_FINGER_PIN | 0x19 | mobile + finger + PIN |
  || BS2_SUB_EVENT_VERIFY_MOBILE_CARD_FACE | 0x1A | mobile + face |
  || BS2_SUB_EVENT_VERIFY_MOBILE_CARD_FACE_PIN | 0x1B | mobile + face + PIN |
  || BS2_SUB_EVENT_VERIFY_MOBILE_CARD_FACE_FINGER | 0x20 | mobile + face + finger |
  || BS2_SUB_EVENT_VERIFY_MOBILE_CARD_FINGER_FACE | 0x21 | mobile + finger + face |
  || BS2_SUB_EVENT_VERIFY_QR | 0x25 | QR |
  || BS2_SUB_EVENT_VERIFY_QR_PIN | 0x26 | QR + PIN |
  || BS2_SUB_EVENT_VERIFY_QR_FINGER | 0x27 | QR + finger |
  || BS2_SUB_EVENT_VERIFY_QR_FINGER_PIN | 0x28 | QR + finger + PIN |
  || BS2_SUB_EVENT_VERIFY_QR_FACE | 0x29 | QR + face |
  || BS2_SUB_EVENT_VERIFY_QR_FACE_PIN | 0x2A | QR + face + PIN |
  || BS2_SUB_EVENT_VERIFY_QR_FACE_FINGER | 0x2B | QR + face + finger |
  || BS2_SUB_EVENT_VERIFY_QR_FINGER_FACE | 0x2C | QR + finger + face |
  || BS2_SUB_EVENT_VERIFY_LOCKOVERRIDE_CARD | 0x31 | Lock override |
  | Identify | BS2_SUB_EVENT_IDENTIFY_FINGER | 0x01 | fingerprint only |
  || BS2_SUB_EVENT_IDENTIFY_FINGER_PIN | 0x02 | fingerprint + PIN |
  || BS2_SUB_EVENT_IDENTIFY_FACE | 0x03 | face only |
  || BS2_SUB_EVENT_IDENTIFY_FACE_PIN | 0x04 | face + PIN |
  || BS2_SUB_EVENT_IDENTIFY_FACE_FINGER | 0x05 | face + fingerprint |
  || BS2_SUB_EVENT_IDENTIFY_FACE_FINGER_PIN | 0x06 | face + fingerprint + PIN |
  || BS2_SUB_EVENT_IDENTIFY_FINGER_FACE | 0x07 | fingerprint + face |
  || BS2_SUB_EVENT_IDENTIFY_FINGER_FACE_PIN | 0x08 | fingerprint + face + PIN |
  | Dual Auth | BS2_SUB_EVENT_DUAL_AUTH_FAIL_TIMEOUT | 0x01 | timeout for dual authentication |
  || BS2_SUB_EVENT_DUAL_AUTH_FAIL_ACCESS_GROUP | 0x02 | invalid access group for dual authentication |
  | Invalid Credential | BS2_SUB_EVENT_CREDENTIAL_ID | 0x01 | invalid user ID |
  || BS2_SUB_EVENT_CREDENTIAL_CARD | 0x02 | invalid card |
  || BS2_SUB_EVENT_CREDENTIAL_PIN | 0x03 | invalid PIN |
  || BS2_SUB_EVENT_CREDENTIAL_FINGER | 0x04 | invalid fingerprint |
  || BS2_SUB_EVENT_CREDENTIAL_FACE | 0x05 | invalid face |
  || BS2_SUB_EVENT_CREDENTIAL_AOC_PIN | 0x06 | invalid AOC PIN |
  || BS2_SUB_EVENT_CREDENTIAL_AOC_FINGER | 0x07 | invalid AOC fingerprint |
  || BS2_SUB_EVENT_CREDENTIAL_MOBILE_CARD | 0x08 | mobile |
  || BS2_SUB_EVENT_NON_NUMERIC_QR | 0x09 | None numeric QR |
  || BS2_SUB_EVENT_NON_PRINTABLE_QR | 0x0A | None printable QR |
  || BS2_SUB_EVENT_TOO_LONG_QR | 0x0B | Too long QR |
  || BS2_SUB_EVENT_CREDENTIAL_QR | 0x0C | QR |
  || BS2_SUB_EVENT_CREDENTIAL_ACCESS_PIN | 0x0D | Access PIN |
  | Auth Fail | BS2_SUB_EVENT_AUTH_FAIL_INVALID_AUTH_MODE | 0x01 | invalid authentication mode |
  || BS2_SUB_EVENT_AUTH_FAIL_INVALID_CREDENTIAL | 0x02 | invalid credential |
  || BS2_SUB_EVENT_AUTH_FAIL_TIMEOUT | 0x03 | authentication timeout |
  || BS2_SUB_EVENT_AUTH_FAIL_MATCHING_REFUSAL | 0x04 | server matching refused |
  || BS2_SUB_EVENT_AUTH_FAIL_DOOR_LOCKED | 0x06 | Door locked |
  || BS2_SUB_EVENT_AUTH_FAIL_EV2SM_REQUIRED | 0x07 | EV2 Secure Messaging required for the DESFire card |
  | Access Denied | BS2_SUB_EVENT_ACCESS_DENIED_ACCESS_GROUP | 0x01 | invalid access group |
  || BS2_SUB_EVENT_ACCESS_DENIED_DISABLED | 0x02 | disabled user |
  || BS2_SUB_EVENT_ACCESS_DENIED_EXPIRED | 0x03 | expired user |
  || BS2_SUB_EVENT_ACCESS_DENIED_ON_BLACKLIST | 0x04 | blacklisted user |
  || BS2_SUB_EVENT_ACCESS_DENIED_APB | 0x05 | denied by APB rule |
  || BS2_SUB_EVENT_ACCESS_DENIED_TIMED_APB | 0x06 | denied by timed APB rule |
  || BS2_SUB_EVENT_ACCESS_DENIED_SCHEDULED_LOCK | 0x07 | denied by scheduled lock zone |
  || BS2_SUB_EVENT_ACCESS_EXCUSED_APB | 0x08 | APB violation excused |
  || BS2_SUB_EVENT_ACCESS_EXCUSED_TIMED_APB | 0x09 | timed APB violation excused |
  || BS2_SUB_EVENT_ACCESS_DENIED_FACE_DETECTION | 0x0A | face not detected |
  || BS2_SUB_EVENT_ACCESS_DENIED_CAMERA_CAPTURE | 0x0B | image not captured |
  || BS2_SUB_EVENT_ACCESS_DENIED_FAKE_FINGER | 0x0C | fake finger detected |
  || BS2_SUB_EVENT_ACCESS_DENIED_DEVICE_ZONE_ENTRANCE_LIMIT | 0x0D | entrance limit |
  || BS2_SUB_EVENT_ACCESS_DENIED_INTRUSION_ALARM | 0x0E | denied by intrusion alarm zone |
  || BS2_SUB_EVENT_ACCESS_DENIED_INTERLOCK | 0x0F | denied by interlock zone |
  || BS2_SUB_EVENT_ACCESS_EXCUSED_AUTH_LIMIT | 0x10 | authentication limit excused |
  || BS2_SUB_EVENT_ACCESS_DENIED_AUTH_LIMIT | 0x11 | authentication limit |
  || BS2_SUB_EVENT_ACCESS_DENIED_ANTI_TAILGATE | 0x12 | anti tailgate violation |
  || BS2_SUB_EVENT_ACCESS_DENIED_HIGH_TEMPERATURE | 0x13 | too high temperature |
  || BS2_SUB_EVENT_ACCESS_DENIED_NO_TEMPERATURE | 0x14 | temperature not detected |
  || BS2_SUB_EVENT_ACCESS_DENIED_UNMASKED_FACE | 0x15 | no mask |
  || BS2_SUB_EVENT_ACCESS_DENIED_OCCUPANCY_LIMIT | 0x16 | Occupancy limit |
  || BS2_SUB_EVENT_ACCESS_DENIED_DOOR_LOCKED | 0x17 | Door locked |
  || BS2_SUB_EVENT_ACCESS_DENIED_DISABLED_ACCESS_GROUP | 0x19 | disabled access group |
  || BS2_SUB_EVENT_ACCESS_DENIED_DISABLED_ACCESS_LEVEL | 0x1A | disabled access level |
  || BS2_SUB_EVENT_ACCESS_DENIED_DISABLED_FLOOR_LEVEL | 0x1B | disabled floor level |
  | Bypass | BS2_SUB_EVENT_BYPASS_THERMAL | 0x01 | temperature |
  || BS2_SUB_EVENT_BYPASS_MASK | 0x02 | mask |
  || BS2_SUB_EVENT_BYPASS_MASK_THERMAL | 0x03 | mask & temperature |
  | Bypass Fail | BS2_SUB_EVENT_HIGH_TEMPERATURE | 0x00 | high temperature |
  || BS2_SUB_EVENT_NO_TEMPERATURE | 0x01 | temperature not detected |
  || BS2_SUB_EVENT_UNMASKED_FACE | 0x02 | mask not detected |
  | Door Flag | BS2_SUB_EVENT_DOOR_FLAG_SCHEDULE | 0x01 | schedule |
  || BS2_SUB_EVENT_DOOR_FLAG_EMERGENCY | 0x02 | emergency |
  || BS2_SUB_EVENT_DOOR_FLAG_OPERATOR | 0x04 | operator |
  || BS2_SUB_EVENT_DOOR_FLAG_LOCKOVERRIDE | 0x05 | lock override |
  | APB | BS2_SUB_EVENT_ZONE_HARD_APB | 0x01 | access denied |
  || BS2_SUB_EVENT_ZONE_SOFT_APB | 0x02 | access allowed |
  | Floor Flag | BS2_SUB_EVENT_FLOOR_FLAG_SCHEDULE | 0x01 | schedule |
  || BS2_SUB_EVENT_FLOOR_FLAG_EMERGENCY | 0x02 | emergency |
  || BS2_SUB_EVENT_FLOOR_FLAG_OPERATOR | 0x04 | operator |
  || BS2_SUB_EVENT_FLOOR_FLAG_ACTION | 0x08 | action |
  | Update Fail | BS2_SUB_EVENT_UPDATE_FAIL_INVALID_FACE | 0x01 | invalid face |
  || BS2_SUB_EVENT_UPDATE_FAIL_MISMATCHED_FORMAT | 0x02 | mismatched format |
  || BS2_SUB_EVENT_UPDATE_FAIL_FULL_CREDENTIAL | 0x03 | full credential |
  || BS2_SUB_EVENT_UPDATE_FAIL_INVALID_USER | 0x04 | invalid user |
  || BS2_SUB_EVENT_UPDATE_FAIL_INTERNAL_ERROR | 0x09 | internal error |
  | By Where | BS2_SUB_EVENT_USER_BY_SERVER | 0x01 | by server |
  || BS2_SUB_EVENT_USER_BY_DEVICE | 0x02 | by device |
  | Device IO | BS2_SUB_EVENT_DEVICE_IO_INPUT_ON | 0x00 | Input on |
  || BS2_SUB_EVENT_DEVICE_IO_INPUT_OFF | 0x01 | Input off |
  || BS2_SUB_EVENT_DEVICE_IO_OUTPUT_ON | 0x10 | Output on |
  || BS2_SUB_EVENT_DEVICE_IO_OUTPUT_OFF | 0x11 | Output off |
  | Relay Action | BS2_SUB_EVENT_RELAY_ACTION_INPUT_OFF | 0x00 | Input off |
  || BS2_SUB_EVENT_RELAY_ACTION_INPUT_ON | 0x01 | Input on |
  || BS2_SUB_EVENT_RELAY_ACTION_SUPERVISED_INPUT_SHORT | 0x02 | Supervised input short |
  || BS2_SUB_EVENT_RELAY_ACTION_SUPERVISED_INPUT_OPEN | 0x03 | Supervised input open |
  || BS2_SUB_EVENT_RELAY_ACTION_TAMPER_ON | 0x05 | Tamper on |
  || BS2_SUB_EVENT_RELAY_ACTION_TAMPER_OFF | 0x06 | Tamper off |
  || BS2_SUB_EVENT_RELAY_ACTION_RS485_CONNECTION | 0x07 | RS485 connected |
  || BS2_SUB_EVENT_RELAY_ACTION_RS485_DISCONNECTION | 0x08 | RS485 disconnected |
  || BS2_SUB_EVENT_RELAY_ACTION_REQ_BY_OPERATOR | 0x09 | Requested by operator |
   {: #SubCode}

TNAKey
: When a T&A key is selected for an authentication event, this field will be set to the key. Refer to [TNA.Key]({{'/api/tna/' | relative_url}}#Key).

hasImage
: True if the event has an image log related to it. You can read image logs using [GetImageLog](#getimagelog).

changedOnDevice
: True if the user is enrolled, changed, or deleted at the device. 

temperature
: Temperature of the user. Refer to [Thermal API]({{'/api/thermal/' | relative_url}}) for the related options.

cardData
: If [eventCode](#EventCode) is BS2_EVENT_VERIFY_FAIL and [subCode](#SubCode) is BS2_SUB_EVENT_CREDENTIAL_CARD, this field will have the failed card data.

[inputInfo](#DetectInputInfo)
: If [eventCode](#EventCode) is BS2_EVENT_DEVICE_INPUT_DETECTED, BS2_EVENT_SUPERVISED_INPUT_SHORT, or BS2_EVENT_SUPERVISED_INPUT_OPEN, it will have additional information about the input port.

[alarmZoneInfo](#AlarmZoneInfo)
: If [eventCode](#EventCode) is BS2_EVENT_ZONE_INTRUSION_ALARM_ARM_FAIL or BS2_EVENT_ZONE_INTRUSION_ALARM, it will have additional information about the alarm zone.

[interlockZoneInfo](#InterlockZoneInfo)
: If [eventCode](#EventCode) is BS2_EVENT_ZONE_INTERLOCK_VIOLATION, it will have additional information about the interlock zone.

```protobuf
message DetectInputInfo {
  uint32 ioDeviceID;
  uint32 port;
  PortValue value;
}
```
{: #DetectInputInfo}

ioDeviceID
: The device ID of the input port.

port
: The index of the port

[value](#PortValue)
: The detected value of the port.

```protobuf
enum PortValue {
  OPEN = 0;
  CLOSED = 1;
  SUPERVISED_SHORT = 2;
  SUPERVISED_OPEN = 3;
};
```
{: #PortValue}

OPEN
: The port is open.

CLOSED
: The port is closed.

SUPERVISED_SHORT
: The supervised port is short-circuited. 

SUPERVISED_OPEN
: The supervised port is open.

```protobuf
message AlarmZoneInfo {
  uint32 zoneID;
  uint32 doorID;
  uint32 ioDeviceID;
  uint32 port;
}
```
{: #AlarmZoneInfo}

```protobuf
message InterlockZoneInfo {
  uint32 zoneID;
  repeated uint32 doorIDs; 
}
```
{: #InterlockZoneInfo}


### GetLog

Read event logs from a device. You can limit the search range using __startEventID__ and __maxNumOfLog__. For T&A specific APIs, refer to the [T&A API]({{'/api/tna' | relative_url}}#event).

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| startEventID | uint32 | The ID of the first event log to be read. If it is 0, read logs from the start |
| maxNumOfLog | uint32 | The maximum number of logs to be read. If it is 0, try to read all the event logs |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| events | [EventLog[]](#EventLog) | The event logs read from the device |

### [Deprecated] GetLogWithFilter   <!--Deprecated. 2024.04.25  by charlie-->

<!-- You can filter the event logs to be read by setting [EventFilter](#EventFilter). For example, to read events of a specific user, you can set __EventFilter.userID__.

```protobuf
message EventFilter {
  string userID;
  uint32 startTime;
  uint32 endTime;
  uint32 eventCode;
  tna.Key TNAKey;
}
```
{: #EventFilter}

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device  |
| startEventID | uint32 | The ID of the first event log to be read. If it is 0, read logs from the start |
| maxNumOfLog | uint32 | The maximum number of logs to be read. If it is 0, try to read all the event logs |
| filters | [EventFilter[]](#EventFilter) | The filters to be applied to the event logs |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| events | [EventLog[]](#EventLog) | The filtered event logs read from the device | -->

***[Important]<BR>It is recommended that logs be received in bulk from the device using the GetLog API, and that logs occurring after the current time be received in real time using the EnableMonitoring and SubscribeRealtimeLog APIs, so that the server stores all logs in an appropriate DBMS and filters the logs from the DBMS.***

### ClearLog

Delete all event logs stored on a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

### ClearLogMulti

Delete all event logs stored on multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |


## Image log

Some devices can record an image in addition to an event log. Please check if the device supports [CapabilityInfo.imageLogSupported]({{'/api/device/' | relative_url}}#CapabilityInfo). You have to specify the event types for which an image is recorded using [SetImageFilter](#setimagefilter).

```protobuf
message ImageLog {
  uint32 ID;
  uint32 timestamp;
  uint32 deviceID;
  string userID;
  uint32 eventCode;
  uint32 subCode;
  bytes JPGImage;
}
```
{: #ImageLog}


ID
: 4 byte identifier of the log record. 

timestamp
: In Unix time format. The number of seconds elapsed since January 1, 1970.

[eventCode](#EventCode)
: 16 bit code identifying the event type.

[subCode](#SubCode)
: Some event types have an additional 8 bit code providing auxiliary information.

JPGImage
: The image recorded in JPG file format.


### GetImageLog

Read image logs from a device. You can limit the search range using __startEventID__ and __maxNumOfLog__. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| startEventID | uint32 | The ID of the first image log to be read. If it is 0, read logs from the start  |
| maxNumOfLog | uint32 | The maximum number of logs to be read. If it is 0, try to read all the image logs. Since image logs are quite larger than textual event logs, you had better set this parameter to a non-zero value |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| imageEvents | [ImageLog[]](#ImageLog) | The image logs read from the device |

### GetImageFilter

Get the current image filters which specify event types for recording images.

```protobuf
message ImageFilter {
  uint32 eventCode; 
  uint32 scheduleID;
}
```
{: #ImageFilter}

[eventCode](#EventCode)
: Event type for which an image will be recorded

scheduleID
: You can limit the recording further by specifying a schedule. Refer to [Schedule]({{'/api/schedule/' | relative_url}}#Schedule).

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| filters | [ImageFilter[]](#ImageFilter) | The filters set to the device |

### SetImageFilter

Set image filters which specify event types for recording images. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| filters | [ImageFilter[]](#ImageFilter) | The filters to be set to the device |

### SetImageFilterMulti 

Set image filters to multiple devices. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| filters | [ImageFilter[]](#ImageFilter) | The filters to be set to the devices |

## Monitoring

To receive real-time events, you have to do the following steps.

1. Enable monitoring on some devices using [EnableMonitoring](#enablemonitoring) or [EnableMonitoringMulti](#enablemonitoringmulti).
2. Subscribe to an event channel using [SubscribeRealtimeLog](#subscriberealtimelog).

### EnableMonitoring

Enable monitoring on a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

### EnableMonitoringMulti

Enable monitoring on multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |

### DisableMonitoring

Disable monitoring on a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |


### DisableMonitoringMulti

Disable monitoring on multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |

### SubscribeRealtimeLog

```protobuf
message SubscribeRealtimeLogRequest {
  int32 queueSize;
  repeated uint32 deviceIDs;
  repeated int32 eventCodes;
}
```
{: #SubscribeRealtimeLogRequest}

queueSize
: If the queue is full, the gateway will discard the real-time events. So, it should be large enough for receiving concurrent events.

deviceIDs
: If it is not empty, receive events from the specified devices only.

[eventCodes](#EventCode)
: If it is not empty, receive the specified events only.

The way of receiving real-time events will vary according to your selected language. Please refer to the quick start guide for details. 

## Device IO States

[+ 1.9.0] You can check the states ​​for the device I/O ports:
* Input/Output, Aux input/output, Relay, Tamper

```protobuf
message IOStates {
  repeated PortValue states = 1;
}
```
{: #IOStates}

[states](#PortValue)
: States of each ports

```protobuf
message DeviceIOStates {
  uint32 deviceID = 1;

  IOStates input = 2;
  IOStates output = 3;
  IOStates relay = 4;
  IOStates tamper = 5;
  IOStates auxIn = 6;
  IOStates auxOut = 7;

  repeated uint32 supervisorInputPortIndex = 8;
}
```
{: #DeviceIOStates}

input
: Input ports including supervised input ports

output
: States of output ports of device

relay
: States of relays of device

tamper
: States of tamper of device

auxIn
: States of aux input ports of device

auxOut
: States of aux output ports of device

supervisorInputPortIndex
: Which of the listed input ports is the supervised input port and has its index.

### GetDeviceIOStates

Get states of device I/O ports

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| slaveIDs | uint32 | Devices can be identified using slaveIDs. If this information is omitted, the status of master and all slave devices is retrieved. |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| states | (#DeviceIOStatus) | I/O port states of devices |
