# 學習計劃系統 - 學生版 API 接口文檔

## 概述

本文檔定義了學習計劃系統學生版的所有API接口，包括用戶管理、學習計劃、進度追蹤、個人設定等功能模組。

### 基礎資訊
- **基礎URL**: `https://api.studyplan.com/v1`
- **認證方式**: Bearer Token
- **數據格式**: JSON
- **字符編碼**: UTF-8

### 通用響應格式

#### 成功響應
```json
{
  "success": true,
  "data": {},
  "message": "操作成功",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

#### 錯誤響應
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "錯誤描述",
    "details": {}
  },
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### 通用錯誤碼
| 錯誤碼 | 描述 |
|--------|------|
| 400 | 請求參數錯誤 |
| 401 | 未授權訪問 |
| 403 | 權限不足 |
| 404 | 資源不存在 |
| 422 | 數據驗證失敗 |
| 500 | 服務器內部錯誤 |

---

## 1. 用戶認證模組

### 1.1 學生登入
**POST** `/auth/student/login` -> liff login, 可能不需要這種登入方式

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| student_id | string | 是 | 學生學號 | "2024001" |
| password | string | 是 | 登入密碼 | "password123" |
| device_token | string | 否 | 設備令牌，用於推送通知 | "device_token_123" |

#### 請求範例
```json
{
  "student_id": "2024001",
  "password": "password123",
  "device_token": "device_token_123"
}
```

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.token | string | 訪問令牌 | "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |
| data.refresh_token | string | 刷新令牌 | "refresh_token_string" |
| data.expires_in | integer | 令牌有效期（秒） | 3600 |
| data.user.id | string | 用戶唯一標識 | "user_123" |
| data.user.student_id | string | 學生學號 | "2024001" |
| data.user.name | string | 學生姓名 | "王小明" |
| data.user.grade | string | 年級 | "國中二年級" |
| data.user.school | string | 學校名稱 | "光明國中" |
| data.user.class | string | 班級 | "二年三班" |
| data.user.avatar_url | string | 頭像URL | "https://example.com/avatar.jpg" |
| data.user.created_at | string | 帳號創建時間 | "2024-01-01T00:00:00Z" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "refresh_token_string",
    "expires_in": 3600,
    "user": {
      "id": "user_123",
      "student_id": "2024001",
      "name": "王小明",
      "grade": "國中二年級",
      "school": "光明國中",
      "class": "二年三班",
      "avatar_url": "https://example.com/avatar.jpg",
      "created_at": "2024-01-01T00:00:00Z"
    }
  }
}
```

## 2. 首頁模組

### 2.1 獲取首頁數據
**GET** `/home/dashboard`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 查詢參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| date | string | 否 | 查詢日期 (YYYY-MM-DD)，預設為今天 | "2024-01-15" |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.user_info.name | string | 用戶姓名 | "王小明" |
| data.user_info.avatar_url | string | 頭像URL | "https://example.com/avatar.jpg" |
| data.user_info.current_date | string | 當前日期 | "2024-01-15" |
| data.user_info.day_of_week | string | 星期幾 | "星期一" |
| data.weekly_progress.completed_hours | integer | 已完成學習時數 | 42 |
| data.weekly_progress.total_hours | integer | 總計劃學習時數 | 50 |
| data.weekly_progress.percentage | integer | 完成百分比 | 84 |
| data.weekly_progress.progress_bar_color | string | 進度條顏色 | "#007aff" |
| data.monthly_calendar.month | string | 月份標題 | "5月概覽" |
| data.monthly_calendar.dates[].date | string | 日期 | "2024-05-01" |
| data.monthly_calendar.dates[].day | string | 日期數字 | "1" |
| data.monthly_calendar.dates[].weekday | string | 星期 | "三" |
| data.monthly_calendar.dates[].has_study_plan | boolean | 是否有學習計劃 | true |
| data.monthly_calendar.dates[].has_exam | boolean | 是否有考試 | false |
| data.monthly_calendar.dates[].is_today | boolean | 是否為今天 | false |
| data.monthly_calendar.dates[].status | string | 學習狀態 | "completed" |
| data.today_plans[].id | string | 計劃ID | "plan_123" |
| data.today_plans[].subject | string | 科目 | "數學" |
| data.today_plans[].title | string | 計劃標題 | "函數與圖形" |
| data.today_plans[].time_range | string | 時間範圍 | "17:30-18:30" |
| data.today_plans[].description | string | 計劃描述 | "觀看教學影片並完成練習題" |
| data.today_plans[].content_type | string | 內容類型 | "video" |
| data.today_plans[].estimated_time | string | 預估時間 | "20分鐘影片" |
| data.today_plans[].status | string | 計劃狀態 | "upcoming" |
| data.today_plans[].start_button_enabled | boolean | 是否可開始學習 | true |
| data.upcoming_exams[].id | string | 考試ID | "exam_123" |
| data.upcoming_exams[].name | string | 考試名稱 | "數學期中考試" |
| data.upcoming_exams[].date | string | 考試日期 | "2024-05-18" |
| data.upcoming_exams[].day_of_week | string | 星期幾 | "星期四" |
| data.upcoming_exams[].scope | string | 考試範圍 | "範圍：第三章至第五章" |
| data.upcoming_exams[].days_remaining | integer | 剩餘天數 | 3 |
| data.upcoming_exams[].countdown_color | string | 倒數顏色 | "#ff9500" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "user_info": {
      "name": "王小明",
      "avatar_url": "https://example.com/avatar.jpg",
      "current_date": "2024-01-15",
      "day_of_week": "星期一"
    },
    "weekly_progress": {
      "completed_hours": 42,
      "total_hours": 50,
      "percentage": 84,
      "progress_bar_color": "#007aff"
    },
    "monthly_calendar": {
      "month": "5月概覽",
      "dates": [
        {
          "date": "2024-05-01",
          "day": "1",
          "weekday": "三",
          "has_study_plan": true,
          "has_exam": false,
          "is_today": false,
          "status": "completed" // completed, in_progress, upcoming
        }
      ]
    },
    "today_plans": [
      {
        "id": "plan_123",
        "subject": "數學",
        "title": "函數與圖形",
        "time_range": "17:30-18:30",
        "description": "觀看教學影片並完成練習題",
        "content_type": "video",
        "estimated_time": "20分鐘影片",
        "status": "upcoming", // completed, in_progress, upcoming
        "start_button_enabled": true
      }
    ],
    "upcoming_exams": [
      {
        "id": "exam_123",
        "name": "數學期中考試",
        "date": "2024-05-18",
        "day_of_week": "星期四",
        "scope": "範圍：第三章至第五章",
        "days_remaining": 3,
        "countdown_color": "#ff9500"
      }
    ]
  }
}
```

---

## 3. 計劃詳情模組

### 3.1 獲取計劃詳情
**GET** `/plans/{plan_id}`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 路徑參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| plan_id | string | 是 | 學習計劃ID | "plan_123" |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.plan_info.id | string | 計劃ID | "plan_123" |
| data.plan_info.title | string | 計劃標題 | "數學期中考試計劃" |
| data.plan_info.exam_date | string | 考試日期 | "2024-05-18" |
| data.plan_info.day_of_week | string | 星期幾 | "星期四" |
| data.plan_info.subject | string | 科目 | "數學" |
| data.plan_info.subject_color | string | 科目顏色 | "#007aff" |
| data.plan_info.progress_percentage | integer | 進度百分比 | 70 |
| data.plan_info.completed_items | integer | 已完成項目數 | 7 |
| data.plan_info.total_items | integer | 總項目數 | 10 |
| data.plan_info.exam_scope | array | 考試範圍 | ["第三章：函數基礎", "第四章：函數圖形"] |
| data.timeline[].id | string | 項目ID | "item_1" |
| data.timeline[].title | string | 項目標題 | "函數基礎概念學習" |
| data.timeline[].description | string | 項目描述 | "學習函數的定義與基本性質" |
| data.timeline[].scheduled_date | string | 預定日期 | "2024-05-10" |
| data.timeline[].status | string | 項目狀態 | "completed" |
| data.timeline[].time_label | string | 時間標籤 | "17:30-18:30" |
| data.timeline[].completion_date | string | 完成時間 | "2024-05-10T18:30:00Z" |
| data.timeline[].is_today | boolean | 是否為今天 | false |
| data.timeline[].can_start | boolean | 是否可以開始 | true |
| data.learning_resources[].id | string | 資源ID | "resource_1" |
| data.learning_resources[].type | string | 資源類型 | "pdf" |
| data.learning_resources[].title | string | 資源標題 | "函數講義" |
| data.learning_resources[].file_size | string | 檔案大小 | "2.5MB" |
| data.learning_resources[].download_url | string | 下載URL | "https://example.com/resources/function_notes.pdf" |
| data.learning_resources[].icon | string | 圖標 | "document" |
| data.learning_resources[].duration | string | 影片時長 | "25:30" |
| data.learning_resources[].play_url | string | 播放URL | "https://example.com/videos/function_tutorial.mp4" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "plan_info": {
      "id": "plan_123",
      "title": "數學期中考試計劃",
      "exam_date": "2024-05-18",
      "day_of_week": "星期四",
      "subject": "數學",
      "subject_color": "#007aff",
      "progress_percentage": 70,
      "completed_items": 7,
      "total_items": 10,
      "exam_scope": [
        "第三章：函數基礎",
        "第四章：函數圖形",
        "第五章：函數應用"
      ]
    },
    "timeline": [
      {
        "id": "item_1",
        "title": "函數基礎概念學習",
        "description": "學習函數的定義與基本性質",
        "scheduled_date": "2024-05-10",
        "status": "completed", // completed, in_progress, upcoming
        "time_label": "17:30-18:30",
        "completion_date": "2024-05-10T18:30:00Z",
        "is_today": false
      },
      {
        "id": "item_2",
        "title": "函數圖形練習",
        "description": "練習繪製各種函數圖形",
        "scheduled_date": "2024-05-15",
        "status": "in_progress",
        "time_label": "17:30-18:30",
        "is_today": true,
        "can_start": true
      }
    ],
    "learning_resources": [
      {
        "id": "resource_1",
        "type": "pdf",
        "title": "函數講義",
        "file_size": "2.5MB",
        "download_url": "https://example.com/resources/function_notes.pdf",
        "icon": "document"
      },
      {
        "id": "resource_2",
        "type": "video",
        "title": "函數教學影片",
        "duration": "25:30",
        "play_url": "https://example.com/videos/function_tutorial.mp4",
        "icon": "video"
      }
    ]
  }
}
```

### 3.2 更新計劃項目狀態
**PUT** `/plans/{plan_id}/items/{item_id}/status`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 路徑參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| plan_id | string | 是 | 學習計劃ID | "plan_123" |
| item_id | string | 是 | 計劃項目ID | "item_1" |

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| status | string | 是 | 項目狀態 (completed, in_progress, postponed) | "completed" |
| completion_time | string | 否 | 完成時間 (ISO 8601) | "2024-01-15T18:30:00Z" |
| notes | string | 否 | 備註 | "完成了函數圖形的練習" |

#### 請求範例
```json
{
  "status": "completed",
  "completion_time": "2024-01-15T18:30:00Z",
  "notes": "完成了函數圖形的練習"
}
```

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.item_id | string | 項目ID | "item_1" |
| data.status | string | 更新後的狀態 | "completed" |
| data.updated_at | string | 更新時間 | "2024-01-15T18:30:00Z" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "item_id": "item_1",
    "status": "completed",
    "updated_at": "2024-01-15T18:30:00Z"
  }
}
```

### 3.3 下載學習資源
**GET** `/plans/{plan_id}/resources/{resource_id}/download`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 路徑參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| plan_id | string | 是 | 學習計劃ID | "plan_123" |
| resource_id | string | 是 | 學習資源ID | "resource_1" |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| Content-Type | string | 文件MIME類型 | "application/pdf" |
| Content-Disposition | string | 文件下載設定 | "attachment; filename=\"function_notes.pdf\"" |
| Content-Length | integer | 文件大小（字節） | 2621440 |

#### 響應範例
```
HTTP/1.1 200 OK
Content-Type: application/pdf
Content-Disposition: attachment; filename="function_notes.pdf"
Content-Length: 2621440

[文件內容]
```

---

## 4. 學習進度模組

### 4.1 獲取進度概覽
**GET** `/progress/overview`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 查詢參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| time_range | string | 否 | 時間範圍 (week, month, semester)，預設為 month | "month" |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.key_metrics[].title | string | 指標標題 | "學習連續天數" |
| data.key_metrics[].value | integer | 指標數值 | 7 |
| data.key_metrics[].unit | string | 數值單位 | "天" |
| data.key_metrics[].color | string | 顯示顏色 | "#ff9500" |
| data.key_metrics[].icon | string | 圖標 | "flame" |
| data.learning_time_chart.time_range | string | 時間範圍 | "本月" |
| data.learning_time_chart.data[].date | string | 日期 | "2024-05-01" |
| data.learning_time_chart.data[].weekday | string | 星期 | "三" |
| data.learning_time_chart.data[].hours | float | 學習時數 | 2.5 |
| data.learning_time_chart.data[].is_today | boolean | 是否為今天 | false |
| data.learning_time_chart.date_range.start | string | 開始日期 | "2024-05-01" |
| data.learning_time_chart.date_range.end | string | 結束日期 | "2024-05-15" |
| data.subject_progress[].subject | string | 科目名稱 | "數學" |
| data.subject_progress[].icon | string | 科目圖標 | "calculator" |
| data.subject_progress[].color | string | 科目顏色 | "#007aff" |
| data.subject_progress[].progress_percentage | integer | 進度百分比 | 85 |
| data.subject_progress[].completed_hours | integer | 已完成時數 | 17 |
| data.subject_progress[].total_hours | integer | 總計劃時數 | 20 |
| data.achievements[].id | string | 成就ID | "achievement_1" |
| data.achievements[].title | string | 成就標題 | "學習超人" |
| data.achievements[].description | string | 成就描述 | "連續學習滿7天" |
| data.achievements[].icon | string | 成就圖標 | "flame" |
| data.achievements[].color | string | 成就顏色 | "#ff9500" |
| data.achievements[].progress_percentage | integer | 完成百分比 | 100 |
| data.achievements[].is_completed | boolean | 是否已完成 | true |
| data.achievements[].completed_at | string | 完成時間 | "2024-01-10T00:00:00Z" |
| data.achievements[].current_value | integer | 當前進度值 | 40 |
| data.achievements[].target_value | integer | 目標值 | 50 |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "key_metrics": [
      {
        "title": "學習連續天數",
        "value": 7,
        "unit": "天",
        "color": "#ff9500",
        "icon": "flame"
      },
      {
        "title": "本週學習時間",
        "value": 42,
        "unit": "小時",
        "color": "#af52de",
        "icon": "clock"
      },
      {
        "title": "計劃完成率",
        "value": 85,
        "unit": "%",
        "color": "#34c759",
        "icon": "checkmark"
      }
    ],
    "learning_time_chart": {
      "time_range": "本月",
      "data": [
        {
          "date": "2024-05-01",
          "weekday": "三",
          "hours": 2.5,
          "is_today": false
        },
        {
          "date": "2024-05-02",
          "weekday": "四",
          "hours": 3.0,
          "is_today": false
        }
      ],
      "date_range": {
        "start": "2024-05-01",
        "end": "2024-05-15"
      }
    },
    "subject_progress": [
      {
        "subject": "數學",
        "icon": "calculator",
        "color": "#007aff",
        "progress_percentage": 85,
        "completed_hours": 17,
        "total_hours": 20
      },
      {
        "subject": "英文",
        "icon": "language",
        "color": "#34c759",
        "progress_percentage": 65,
        "completed_hours": 13,
        "total_hours": 20
      },
      {
        "subject": "科學",
        "icon": "flask",
        "color": "#ff9500",
        "progress_percentage": 40,
        "completed_hours": 8,
        "total_hours": 20
      }
    ],
    "achievements": [
      {
        "id": "achievement_1",
        "title": "學習超人",
        "description": "連續學習滿7天",
        "icon": "flame",
        "color": "#ff9500",
        "progress_percentage": 100,
        "is_completed": true,
        "completed_at": "2024-01-10T00:00:00Z"
      },
      {
        "id": "achievement_2",
        "title": "學習時間達人",
        "description": "累積學習時間達50小時",
        "icon": "clock",
        "color": "#af52de",
        "progress_percentage": 80,
        "is_completed": false,
        "current_value": 40,
        "target_value": 50
      },
      {
        "id": "achievement_3",
        "title": "完成大師",
        "description": "完成5個學習計劃",
        "icon": "checkmark",
        "color": "#34c759",
        "progress_percentage": 60,
        "is_completed": false,
        "current_value": 3,
        "target_value": 5
      }
    ]
  }
}
```

### 4.2 獲取詳細學習記錄
**GET** `/progress/learning-records`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 查詢參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| date | string | 否 | 查詢日期 (YYYY-MM-DD) | "2024-01-15" |
| subject | string | 否 | 科目篩選 | "數學" |
| page | integer | 否 | 頁碼，預設為1 | 1 |
| limit | integer | 否 | 每頁數量，預設為20 | 20 |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.records[].id | string | 記錄ID | "record_123" |
| data.records[].plan_id | string | 計劃ID | "plan_123" |
| data.records[].subject | string | 科目 | "數學" |
| data.records[].title | string | 學習標題 | "函數與圖形" |
| data.records[].start_time | string | 開始時間 | "2024-01-15T17:30:00Z" |
| data.records[].end_time | string | 結束時間 | "2024-01-15T18:30:00Z" |
| data.records[].duration_minutes | integer | 學習時長（分鐘） | 60 |
| data.records[].status | string | 學習狀態 | "completed" |
| data.records[].notes | string | 學習備註 | "完成了函數圖形的練習" |
| data.pagination.current_page | integer | 當前頁碼 | 1 |
| data.pagination.total_pages | integer | 總頁數 | 5 |
| data.pagination.total_records | integer | 總記錄數 | 100 |
| data.pagination.has_next | boolean | 是否有下一頁 | true |
| data.pagination.has_prev | boolean | 是否有上一頁 | false |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "records": [
      {
        "id": "record_123",
        "plan_id": "plan_123",
        "subject": "數學",
        "title": "函數與圖形",
        "start_time": "2024-01-15T17:30:00Z",
        "end_time": "2024-01-15T18:30:00Z",
        "duration_minutes": 60,
        "status": "completed",
        "notes": "完成了函數圖形的練習"
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 5,
      "total_records": 100,
      "has_next": true,
      "has_prev": false
    }
  }
}
```

### 4.3 分享成就
**POST** `/progress/achievements/{achievement_id}/share`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 路徑參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| achievement_id | string | 是 | 成就ID | "achievement_123" |

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| platform | string | 是 | 分享平台 (wechat, facebook, twitter) | "wechat" |

#### 請求範例
```json
{
  "platform": "wechat"
}
```

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.share_url | string | 分享連結 | "https://example.com/share/achievement_123" |
| data.qr_code_url | string | 二維碼圖片URL | "https://example.com/qr/achievement_123.png" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "share_url": "https://example.com/share/achievement_123",
    "qr_code_url": "https://example.com/qr/achievement_123.png"
  }
}
```

---

## 5. 個人資料模組

### 5.1 獲取個人資料
**GET** `/profile/info`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.basic_info.name | string | 學生姓名 | "王小明" |
| data.basic_info.grade | string | 年級 | "國中二年級" |
| data.basic_info.school | string | 學校名稱 | "光明國中" |
| data.basic_info.class | string | 班級 | "二年三班" |
| data.basic_info.avatar_url | string | 頭像URL | "https://example.com/avatar.jpg" |
| data.study_time_settings.weekdays.monday | array | 週一學習時段 | ["17:30-18:30", "19:00-20:00"] |
| data.study_time_settings.weekdays.tuesday | array | 週二學習時段 | ["17:30-18:30", "19:00-20:00"] |
| data.study_time_settings.weekdays.wednesday | array | 週三學習時段 | ["17:30-18:30", "19:00-20:00"] |
| data.study_time_settings.weekdays.thursday | array | 週四學習時段 | ["17:30-18:30", "19:00-20:00"] |
| data.study_time_settings.weekdays.friday | array | 週五學習時段 | ["17:30-18:30", "19:00-20:00"] |
| data.study_time_settings.weekend.saturday | array | 週六學習時段 | ["09:00-11:00", "16:00-18:00"] |
| data.study_time_settings.weekend.sunday | array | 週日學習時段 | ["09:00-11:00", "14:00-16:00"] |
| data.extracurricular_activities[].id | string | 活動ID | "activity_1" |
| data.extracurricular_activities[].name | string | 活動名稱 | "數學補習班" |
| data.extracurricular_activities[].schedule.days | array | 活動日期 | ["tuesday", "thursday"] |
| data.extracurricular_activities[].schedule.time | string | 活動時間 | "19:00-21:00" |
| data.special_dates[].id | string | 特殊日期ID | "special_1" |
| data.special_dates[].title | string | 特殊日期標題 | "家庭旅行" |
| data.special_dates[].start_date | string | 開始日期 | "2024-05-20" |
| data.special_dates[].end_date | string | 結束日期 | "2024-05-25" |
| data.notification_settings.daily_reminder | boolean | 每日學習提醒 | true |
| data.notification_settings.progress_report | boolean | 進度報告 | true |
| data.notification_settings.exam_reminder | boolean | 考試提醒 | true |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "basic_info": {
      "name": "王小明",
      "grade": "國中二年級",
      "school": "光明國中",
      "class": "二年三班",
      "avatar_url": "https://example.com/avatar.jpg"
    },
    "study_time_settings": {
      "weekdays": {
        "monday": ["17:30-18:30", "19:00-20:00", "20:00-21:00"],
        "tuesday": ["17:30-18:30", "19:00-20:00", "20:00-21:00"],
        "wednesday": ["17:30-18:30", "19:00-20:00", "20:00-21:00"],
        "thursday": ["17:30-18:30", "19:00-20:00", "20:00-21:00"],
        "friday": ["17:30-18:30", "19:00-20:00", "20:00-21:00"]
      },
      "weekend": {
        "saturday": ["09:00-11:00", "16:00-18:00"],
        "sunday": ["09:00-11:00", "14:00-16:00"]
      }
    },
    "extracurricular_activities": [
      {
        "id": "activity_1",
        "name": "數學補習班",
        "schedule": {
          "days": ["tuesday", "thursday"],
          "time": "19:00-21:00"
        }
      },
      {
        "id": "activity_2",
        "name": "鋼琴課",
        "schedule": {
          "days": ["wednesday"],
          "time": "18:00-20:00"
        }
      }
    ],
    "special_dates": [
      {
        "id": "special_1",
        "title": "家庭旅行",
        "start_date": "2024-05-20",
        "end_date": "2024-05-25"
      }
    ],
    "notification_settings": {
      "daily_reminder": true,
      "progress_report": true,
      "exam_reminder": true
    }
  }
}
```

### 5.2 更新個人資料
**PUT** `/profile/info`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| basic_info.name | string | 是 | 學生姓名 | "王小明" |
| basic_info.grade | string | 是 | 年級 | "國中二年級" |
| basic_info.school | string | 是 | 學校名稱 | "光明國中" |
| basic_info.class | string | 是 | 班級 | "二年三班" |

#### 請求範例
```json
{
  "basic_info": {
    "name": "王小明",
    "grade": "國中二年級",
    "school": "光明國中",
    "class": "二年三班"
  }
}
```

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.updated_at | string | 更新時間 | "2024-01-15T10:30:00Z" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "updated_at": "2024-01-15T10:30:00Z"
  }
}
```

### 5.3 上傳頭像
**POST** `/profile/avatar`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |
| Content-Type | string | 是 | 多部分表單數據 | "multipart/form-data" |

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| avatar | file | 是 | 頭像圖片文件 (JPG, PNG, 最大5MB) | [文件上傳] |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.avatar_url | string | 頭像URL | "https://example.com/avatars/user_123.jpg" |
| data.updated_at | string | 更新時間 | "2024-01-15T10:30:00Z" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "avatar_url": "https://example.com/avatars/user_123.jpg",
    "updated_at": "2024-01-15T10:30:00Z"
  }
}
```

### 5.4 更新學習時間設定
**PUT** `/profile/study-time`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| weekdays.monday | array | 否 | 週一學習時段 | ["17:30-18:30", "19:00-20:00"] |
| weekdays.tuesday | array | 否 | 週二學習時段 | ["17:30-18:30", "19:00-20:00"] |
| weekdays.wednesday | array | 否 | 週三學習時段 | ["17:30-18:30", "19:00-20:00"] |
| weekdays.thursday | array | 否 | 週四學習時段 | ["17:30-18:30", "19:00-20:00"] |
| weekdays.friday | array | 否 | 週五學習時段 | ["17:30-18:30", "19:00-20:00"] |
| weekend.saturday | array | 否 | 週六學習時段 | ["09:00-11:00", "16:00-18:00"] |
| weekend.sunday | array | 否 | 週日學習時段 | ["09:00-11:00", "14:00-16:00"] |

#### 請求範例
```json
{
  "weekdays": {
    "monday": ["17:30-18:30", "19:00-20:00"],
    "tuesday": ["17:30-18:30", "19:00-20:00"],
    "wednesday": ["17:30-18:30", "19:00-20:00"],
    "thursday": ["17:30-18:30", "19:00-20:00"],
    "friday": ["17:30-18:30", "19:00-20:00"]
  },
  "weekend": {
    "saturday": ["09:00-11:00", "16:00-18:00"],
    "sunday": ["09:00-11:00", "14:00-16:00"]
  }
}
```

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.updated_at | string | 更新時間 | "2024-01-15T10:30:00Z" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "updated_at": "2024-01-15T10:30:00Z"
  }
}
```