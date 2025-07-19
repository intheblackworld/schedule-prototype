# 學習計劃系統 - 教師版 API 接口文檔

## 概述

本文檔定義了學習計劃系統教師版的所有API接口，包括學生管理、學習計劃、行事曆、考試管理和學校管理等核心功能模組。

### 基礎信息
- **基礎URL**: `https://api.studyplan.com/v1`
- **認證方式**: Bearer Token
- **數據格式**: JSON
- **字符編碼**: UTF-8

### 通用響應格式
```json
{
  "success": true,
  "code": 200,
  "message": "操作成功",
  "data": {},
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### 錯誤響應格式
```json
{
  "success": false,
  "code": 400,
  "message": "請求參數錯誤",
  "errors": [
    {
      "field": "name",
      "message": "姓名不能為空"
    }
  ],
  "timestamp": "2024-01-15T10:30:00Z"
}
```

---

## 2. 學生管理模組 API

### 2.1 獲取學生列表
**接口**: `GET /students`

**描述**: 獲取學生列表，支援篩選和分頁

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 | 預設值 |
|--------|------|------|------|--------|
| page | integer | 否 | 頁碼 | 1 |
| limit | integer | 否 | 每頁數量 | 12 |
| grade | string | 否 | 年級篩選 | - |
| class_id | string | 否 | 班級ID篩選 | - |
| sort_by | string | 否 | 排序欄位 | name |
| sort_order | string | 否 | 排序方向 | asc |
| search | string | 否 | 搜尋關鍵字（學生姓名） | - |

**參數選項**:
- `sort_by`: completion_rate(完成率) / name(姓名)
- `sort_order`: asc(升序) / desc(降序)

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| students | array | 學生列表 |
| └─ id | string | 學生ID |
| └─ name | string | 學生姓名 |
| └─ student_id | string | 學號 |
| └─ avatar | string | 頭像URL |
| └─ grade | string | 年級 |
| └─ class | string | 班級 |
| └─ status | string | 狀態 |
| └─ completion_rate | integer | 完成率(%) |
| └─ progress_status | string | 進度狀態 |
| └─ contact_info | object | 聯絡資訊 |
| └─ └─ phone | string | 電話 |
| └─ └─ email | string | 電子郵件 |
| pagination | object | 分頁資訊 |
| └─ current_page | integer | 當前頁碼 |
| └─ total_pages | integer | 總頁數 |
| └─ total_count | integer | 總數量 |
| └─ per_page | integer | 每頁數量 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "students": [
      {
        "id": "student_001",
        "name": "王小明",
        "student_id": "S20230023",
        "avatar": "https://example.com/avatar1.jpg",
        "grade": "二年級",
        "class": "二年三班",
        "status": "active",
        "completion_rate": 85,
        "progress_status": "good",
        "contact_info": {
          "phone": "0912345678",
          "email": "student@example.com"
        }
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 26,
      "total_count": 157,
      "per_page": 6
    }
  }
}
```

### 2.3 獲取學生詳情
**接口**: `GET /students/{student_id}`

**描述**: 獲取指定學生的詳細資訊

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| student_id | string | 是 | 學生ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| id | string | 學生ID |
| name | string | 學生姓名 |
| student_id | string | 學號 |
| avatar | string | 頭像URL |
| grade | string | 年級 |
| class | string | 班級 |
| status | string | 狀態 |
| contact_info | object | 聯絡資訊 |
| └─ phone | string | 電話號碼 |
| └─ email | string | 電子郵件 |
| └─ address | string | 地址 |
| learning_plans | array | 學習計劃列表 |
| └─ id | string | 計劃ID |
| └─ name | string | 計劃名稱 |
| └─ subject | string | 科目 |
| └─ completion_rate | integer | 完成率(%) |
| └─ status | string | 狀態 |
| exam_records | array | 考試記錄 |
| └─ exam_name | string | 考試名稱 |
| └─ score | integer | 分數 |
| └─ date | string | 考試日期 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "id": "student_001",
    "name": "王小明",
    "student_id": "S20230023",
    "avatar": "https://example.com/avatar1.jpg",
    "grade": 2,
    "status": "active",
    // "contact_info": {
    //   "phone": "0912345678",
    //   "email": "student@example.com",
    //   "address": "台北市中正區..."
    // },
    "activities": [
      {
        "name": "數學補習班",
        "schedule": {
          "days": ["Mon", "Tue"],
          "time": "19:00-21:00"
        }
      }
    ],
    "specials": [
      {
        "name": "家庭旅行",
        "schedule": {
          "date": "2025-05-20",
          "time": "19:00-21:00"
        }
      }
    ],
    "last_subject_scores": [
      {
        "date": "2024-05-20",
        "scores": [
          {
            "subject": "數學",
            "score": 85,
          },
          {
            "subject": "英文",
            "score": 90,
          },
          ...
        ]
      },
      {
        ...
      }
    ],  
    "learning_plans": [
      {
        "id": "plan_001",
        "name": "期中考準備計劃",
        // "subject": "數學",
        "completion_rate": 85,
        "status": "in_progress"
      }
    ],
    // "exam_records": [
    //   {
    //     "exam_name": "數學月考",
    //     "score": 85,
    //     "date": "2024-04-20"
    //   }
    // ]
  }
}
```

<!-- ### 2.4 更新學生資訊
**接口**: `PUT /students/{student_id}`

**描述**: 更新學生資訊

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| student_id | string | 是 | 學生ID |

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| name | string | 否 | 學生姓名 |
| grade | string | 否 | 年級 |
| class_id | string | 否 | 班級ID |
| contact_info | object | 否 | 聯絡資訊 |
| └─ phone | string | 否 | 電話號碼 |
| └─ email | string | 否 | 電子郵件 |

**請求體**:
```json
{
  "name": "王小明",
  "grade": 2,
  // "class_id": "class_003",
  // "contact_info": {
  //   "phone": "0912345678",
  //   "email": "updated@example.com"
  // }
}
``` -->

<!-- ### 2.5 刪除學生
**接口**: `DELETE /students/{student_id}`

**描述**: 刪除學生（軟刪除）

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| student_id | string | 是 | 學生ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| success | boolean | 操作是否成功 |
| message | string | 操作結果訊息 |

--- -->

## 3. 學習計劃模組 API

### 3.1 獲取學習計劃列表
**接口**: `GET /reading-plans`

**描述**: 獲取學習計劃列表

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 | 預設值 |
|--------|------|------|------|--------|
| page | integer | 否 | 頁碼 | 1 |
| limit | integer | 否 | 每頁數量 | 12 |
| subject | string | 否 | 科目篩選 | - |
| class_id | string | 否 | 班級篩選 | - |
| status | string | 否 | 狀態篩選 | - |
| search | string | 否 | 搜尋關鍵字 | - |

**參數選項**:
- `status`: draft(草稿) / in_progress(進行中) / completed(已完成)

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| plans | array | 計劃列表 |
| └─ id | string | 計劃ID |
| └─ name | string | 計劃名稱 |
| └─ subject | string | 科目 |
| └─ status | string | 狀態 |
| └─ grade | string | 年級 |
| └─ exam_date | string | 考試日期 |
| └─ completion_rate | integer | 完成率(%) |
| └─ assigned_students | integer | 指派學生數 |
| └─ created_at | string | 創建時間 |
| └─ created_by | string | 創建者ID |
| pagination | object | 分頁資訊 |
| └─ current_page | integer | 當前頁碼 |
| └─ total_pages | integer | 總頁數 |
| └─ total_count | integer | 總數量 |
| └─ per_page | integer | 每頁數量 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "plans": [
      {
        "id": "plan_001",
        "name": "李小芳學習計劃",
        // "subject": "數學",
        "status": "in_progress",
        "grade": 2,
        "exam_date": "2024-05-18",
        "completion_rate": 75,
        // "assigned_students": 82,
        "created_at": "2024-04-15T10:30:00Z",
        "created_by": "teacher_001"
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 5,
      "total_count": 42,
      "per_page": 12
    }
  }
}
```

### 3.2 創建學習計劃
**接口**: `POST /reading-plans`

**描述**: 創建新的學習計劃

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| name | string | 是 | 計劃名稱 |
| subject | string | 是 | 科目 |
| grade | string | 是 | 年級 |
| exam_date | string | 是 | 考試日期 |
| description | string | 否 | 計劃描述 |
| chapters | array | 是 | 章節列表 |
| └─ chapter_id | string | 是 | 章節ID |
| └─ name | string | 是 | 章節名稱 |
| └─ resources | array | 否 | 學習資源 |
| └─ └─ type | string | 是 | 資源類型 |
| └─ └─ title | string | 是 | 資源標題 |
| └─ └─ url | string | 否 | 資源URL |
| └─ └─ duration | integer | 否 | 影片時長(秒) |
| └─ └─ question_count | integer | 否 | 題目數量 |
| target_students | array | 否 | 目標學生ID列表 |
| goals | object | 否 | 學習目標 |
| └─ completion_rate | integer | 否 | 目標完成率(%) |
| └─ target_score | integer | 否 | 目標分數 |

**參數選項**:
- `resources.type`: video(影片) / exercise(練習題) / document(文件)

**請求體**:
```json
{
  "name": "期中考準備計劃",
  // "subject": "數學",
  "grade": 2,
  "description": "針對期中考的系統性複習計劃",
  "school_id": "school_001",
  "student_id": "",
  "hour_time": [
    1,
    2,
    3,
    3,
    2,
    4,
    6
  ],
  "activity_ids": [
    "activity_001",
    "activity_002"
  ],
  "special_ids": [
    "special_001",
    "special_002"
  ],
  "main_subject": ["數學", "自然科學"],
  "chapters_ids": [
    "xxx",
    "xxx",
  ],
  "version_ids": [
    "xxx",
    "xxx",
  ],
  "got_home_time": "2024-05-20T19:00:00Z",
  "available_contact_time": "2024-05-20T19:00:00Z",
  "exam_name": "考試名稱",
  "exam_date": "2024-05-18",
  // "chapters": [
  //   {
  //     "chapter_id": "chapter_001",
  //     "name": "第三章 二次函數",
  //     "resources": [
  //       {
  //         "type": "video",
  //         "title": "二次函數基礎概念",
  //         "url": "https://example.com/video1.mp4",
  //         "duration": 1200
  //       },
  //       {
  //         "type": "exercise",
  //         "title": "二次函數練習題",
  //         "question_count": 20
  //       }
  //     ]
  //   }
  // ],
  // "target_students": ["student_001", "student_002"],
  // "goals": {
  //   "completion_rate": 80,
  //   "target_score": 85
  // }
}
```

### 3.3 獲取學習計劃詳情
**接口**: `GET /reading-plans/{plan_id}`

**描述**: 獲取學習計劃詳細資訊

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| plan_id | string | 是 | 計劃ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| id | string | 計劃ID |
| name | string | 計劃名稱 |
| subject | string | 科目 |
| status | string | 狀態 |
| grade | string | 年級 |
| exam_date | string | 考試日期 |
| description | string | 計劃描述 |
| completion_rate | integer | 完成率(%) |
| chapters | array | 章節列表 |
| └─ id | string | 章節ID |
| └─ name | string | 章節名稱 |
| └─ completion_rate | integer | 完成率(%) |
| └─ resources | array | 學習資源 |
| └─ └─ id | string | 資源ID |
| └─ └─ type | string | 資源類型 |
| └─ └─ title | string | 資源標題 |
| └─ └─ url | string | 資源URL |
| └─ └─ duration | integer | 影片時長(秒) |
| └─ └─ completion_rate | integer | 完成率(%) |
| student_progress | array | 學生進度 |
| └─ student_id | string | 學生ID |
| └─ name | string | 學生姓名 |
| └─ completion_rate | integer | 完成率(%) |
| └─ last_activity | string | 最後活動時間 |
| created_at | string | 創建時間 |
| created_by | string | 創建者ID |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "id": "plan_001",
    "name": "期中考準備計劃",
    // "subject": "數學",
    "status": "in_progress",
    "grade": 2,
    "exam_date": "2024-05-18",
    "description": "針對期中考的系統性複習計劃",
    "completion_rate": 75,
    "got_home_time": "2024-05-20T19:00:00Z",
    "available_contact_time": "2024-05-20T19:00:00Z",
    "chapters": [
      {
        "id": "chapter_001",
        "name": "第三章 二次函數",
        "completion_rate": 80,
        "resources": [
          {
            "id": "resource_001",
            "type": "video",
            "title": "二次函數基礎概念",
            "url": "https://example.com/video1.mp4",
            "duration": 1200,
            "completion_rate": 85
          }
        ]
      }
    ],
    "student_progress": {
        "student_id": "student_001",
        "name": "王小明",
        "completion_rate": 85,
        "last_activity": "2024-01-15T10:30:00Z"
      },
    "created_at": "2024-04-15T10:30:00Z",
    "created_by": "teacher_001"
  }
}
```
### 3.3.5 獲取學習計劃章節列表

GET /reading-plans/{plan_id}/tasks
data: [
  {
    date: "2024-05-20",
    tasks: [
      {
        id: 'xxx',
        type: 'reading', // break
        subject: "數學",
        title: "函數與圖形",
        book_name: "文法達人(二)"
        chapter_time: 3000, // seconds
        description: "二次函數的圖形與性質",
        status: "completed",
        scheduled_date: "2024-05-20",
        completion_rate: 85,
        remark: "學習很棒，有很多新的知識",
      },
      {
        id: 'xxx',
        type: 'break',
        title: "休息",
        scheduled_date: "2024-05-21",
      },
      {
        id: 'xxx',
        type: 'break',
        title: "家族旅行",
        scheduled_date: "2024-05-21",
      },
      ...
    ]
  }
]

PUT /reading-plans/{plan_id}/tasks/{task_id}
data: {
  task_id: 'xxx',
  subject: "數學",
  title: "函數與圖形",
  book_name: "文法達人(二)"
  chapter_time: 3000, // seconds
  description: "二次函數的圖形與性質",
  status: "completed",
  scheduled_date: "2024-05-20",
  completion_rate: 85,
  remark: "學習很棒，有很多新的知識",
}

### 3.4 更新學習計劃
**接口**: `PUT /reading-plans/{plan_id}`

**描述**: 更新學習計劃資訊

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| plan_id | string | 是 | 計劃ID |

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| name | string | 否 | 計劃名稱 |
| subject | string | 否 | 科目 |
| grade | string | 否 | 年級 |
| exam_date | string | 否 | 考試日期 |
| description | string | 否 | 計劃描述 |
| chapters | array | 否 | 章節列表 |
| target_students | array | 否 | 目標學生ID列表 |
| goals | object | 否 | 學習目標 |

### 3.5 刪除學習計劃
**接口**: `DELETE /reading-plans/{plan_id}`

**描述**: 刪除學習計劃

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| plan_id | string | 是 | 計劃ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| success | boolean | 操作是否成功 |
| message | string | 操作結果訊息 |

## 4. 行事曆模組 API

### 4.1 獲取行事曆事件
**接口**: `GET /calendar/events`

**描述**: 獲取行事曆事件列表

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 | 格式 |
|--------|------|------|------|------|
| start_date | string | 是 | 開始日期 | YYYY-MM-DD |
| end_date | string | 是 | 結束日期 | YYYY-MM-DD |
| school_id | string | 否 | 學校ID | - |
| event_types | array | 否 | 事件類型篩選 | - |
| grade | string | 否 | 年級篩選 | - |

**參數選項**:
- `event_types`: exam(考試) / activity(活動) / holiday(假日) / study_plan(學習計劃)

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| events | array | 事件列表 |
| └─ id | string | 事件ID |
| └─ title | string | 事件標題 |
| └─ type | string | 事件類型 |
| └─ start_date | string | 開始日期 |
| └─ end_date | string | 結束日期 |
| └─ start_time | string | 開始時間 |
| └─ end_time | string | 結束時間 |
| └─ grade | string | 年級 |
| └─ description | string | 事件描述 |
| └─ color | string | 事件顏色 |
| └─ all_day | boolean | 是否全天事件 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "events": [
      {
        "id": "event_001",
        "title": "數學期中考試",
        "type": "exam",
        "start_date": "2024-05-18",
        "end_date": "2024-05-18",
        // "start_time": "09:00",
        // "end_time": "11:00",
        "grade": 2,
        "description": "二年級數學期中考試",
        "color": "#ff4444"
      },
      {
        "id": "event_002",
        "title": "校慶活動",
        "type": "activity",
        "start_date": "2024-05-22",
        "end_date": "2024-05-22",
        // "all_day": true,
        "grade": "全校",
        "description": "學校年度校慶活動",
        "color": "#ff8800"
      }
    ]
  }
}
```

### 4.2 創建行事曆事件
**接口**: `POST /calendar/events`

**描述**: 創建新的事件

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 | 格式 |
|--------|------|------|------|------|
| title | string | 是 | 事件標題 | - |
| type | string | 是 | 事件類型 | - |
| start_date | string | 是 | 開始日期 | YYYY-MM-DD |
| end_date | string | 是 | 結束日期 | YYYY-MM-DD |
| start_time | string | 否 | 開始時間 | HH:MM |
| end_time | string | 否 | 結束時間 | HH:MM |
| grade | string | 否 | 年級 | - |
| description | string | 否 | 事件描述 | - |
| target_students | array | 否 | 目標學生ID列表 | - |
| reminder | object | 否 | 提醒設定 | - |
| └─ enabled | boolean | 否 | 是否啟用提醒 | - |
| └─ minutes_before | integer | 否 | 提前提醒分鐘數 | - |

**參數選項**:
- `type`: exam(考試) / activity(活動) / holiday(假日) / study_plan(學習計劃)

**請求體**:
```json
{
  "title": "數學期中考試",
  "type": "exam",
  "start_date": "2024-05-18",
  "end_date": "2024-05-18",
  // "start_time": "09:00",
  // "end_time": "11:00",
  "grade": 2,
  "description": "二年級數學期中考試",
  // "target_students": ["student_001", "student_002"],
  // "reminder": {
  //   "enabled": true,
  //   "minutes_before": 30
  // }
}
```

### 4.3 更新行事曆事件
**接口**: `PUT /calendar/events/{event_id}`

**描述**: 更新事件資訊

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| event_id | string | 是 | 事件ID |

**請求參數**: 同創建事件

### 4.4 刪除行事曆事件
**接口**: `DELETE /calendar/events/{event_id}`

**描述**: 刪除事件

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| event_id | string | 是 | 事件ID |

### 4.5 獲取即將到來事件
**接口**: `GET /calendar/upcoming`

**描述**: 獲取即將到來的事件列表

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 | 預設值 |
|--------|------|------|------|--------|
| days | integer | 否 | 未來天數 | 7 |
| limit | integer | 否 | 返回數量限制 | 10 |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| events | array | 即將到來事件列表 |
| └─ id | string | 事件ID |
| └─ title | string | 事件標題 |
| └─ type | string | 事件類型 |
| └─ date | string | 事件日期 |
| └─ grade | string | 年級 |
| └─ days_until | integer | 剩餘天數 |

---

---

## 6. 學校管理模組 API

### 6.1 獲取學校列表
**接口**: `GET /schools`

**描述**: 獲取學校列表

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 | 預設值 |
|--------|------|------|------|--------|
| search | string | 否 | 搜尋關鍵字 | - |
| page | integer | 否 | 頁碼 | 1 |
| limit | integer | 否 | 每頁數量 | 20 |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| schools | array | 學校列表 |
| └─ id | string | 學校ID |
| └─ name | string | 學校名稱 |
| └─ address | string | 學校地址 |
| └─ type | string | 學校類型 |
| └─ grade_range | string | 年級範圍 |
| └─ class_count | integer | 班級數量 |
| └─ is_current | boolean | 是否為當前學校 |
| pagination | object | 分頁資訊 |
| └─ current_page | integer | 當前頁碼 |
| └─ total_pages | integer | 總頁數 |
| └─ total_count | integer | 總數量 |
| └─ per_page | integer | 每頁數量 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "schools": [
      {
        "id": "school_001",
        "name": "光明國中",
        "address": "台北市中正區光明路123號",
        "type": "國中",
        "grade_range": "7-9",
        "class_count": 36,
        "is_current": true
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 13,
      "total_count": 247,
      "per_page": 20
    }
  }
}
```

### 6.2 獲取學校詳情
**接口**: `GET /schools/{school_id}`

**描述**: 獲取學校詳細資訊

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| school_id | string | 是 | 學校ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| id | string | 學校ID |
| name | string | 學校名稱 |
| address | string | 學校地址 |
| type | string | 學校類型 |
| grade_range | string | 年級範圍 |
| class_count | integer | 班級數量 |
| student_count | integer | 學生數量 |
| teacher_count | integer | 教師數量 |
| contact_info | object | 聯絡資訊 |
| └─ phone | string | 電話號碼 |
| └─ email | string | 電子郵件 |
| created_at | string | 創建時間 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "id": "school_001",
    "name": "光明國中",
    "address": "台北市中正區光明路123號",
    "type": "國中",
    "grade_range": "7-9",
    "class_count": 36,
    "student_count": 157,
    "teacher_count": 45,
    "contact_info": {
      "phone": "02-12345678",
      "email": "contact@guangming.edu.tw"
    },
    "created_at": "2020-01-01T00:00:00Z"
  }
}
```

### 6.3 獲取科目版本配置
**接口**: `GET /schools/{school_id}/subject-versions`

**描述**: 獲取學校的科目版本配置

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| school_id | string | 是 | 學校ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| grades | array | 年級列表 |
| └─ grade | string | 年級名稱 |
| └─ subjects | array | 科目列表 |
| └─ └─ subject | string | 科目名稱 |
| └─ └─ current_version | string | 當前版本 |
| └─ └─ available_versions | array | 可用版本列表 |
| └─ └─ is_selected | boolean | 是否已選擇 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "grades": [
      {
        "grade": "七年級",
        "subjects": [
          {
            "subject": "國文",
            "current_version": "南一",
            "available_versions": ["南一", "翰林", "康軒"],
            "is_selected": true
          },
          {
            "subject": "英文",
            "current_version": "翰林",
            "available_versions": ["南一", "翰林"],
            "is_selected": false
          }
        ]
      }
    ]
  }
}
```

### 6.4 更新科目版本配置
**接口**: `PUT /schools/{school_id}/subject-versions`

**描述**: 批量更新科目版本配置

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| school_id | string | 是 | 學校ID |

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| updates | array | 是 | 更新列表 |
| └─ grade | string | 是 | 年級 |
| └─ subject | string | 是 | 科目 |
| └─ version | string | 是 | 版本 |

**請求體**:
```json
{
  "updates": [
    {
      "grade": "七年級",
      "subject": "國文",
      "version": "南一"
    },
    {
      "grade": "七年級",
      "subject": "英文", 
      "version": "翰林"
    }
  ]
}
```

### 6.5 獲取教材內容
**接口**: `GET /schools/{school_id}/materials`

**描述**: 獲取學校的教材內容

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| school_id | string | 是 | 學校ID |

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| grade | string | 否 | 年級篩選 |
| subject | string | 否 | 科目篩選 |
| version | string | 否 | 版本篩選 |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| materials | array | 教材列表 |
| └─ id | string | 教材ID |
| └─ name | string | 教材名稱 |
| └─ grade | string | 年級 |
| └─ subject | string | 科目 |
| └─ version | string | 版本 |
| └─ statistics | object | 統計資訊 |
| └─ └─ chapters | integer | 章節數 |
| └─ └─ units | integer | 單元數 |
| └─ └─ videos | integer | 影片數 |
| └─ └─ exercises | integer | 練習題數 |
| └─ last_updated | string | 最後更新時間 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "materials": [
      {
        "id": "material_001",
        "name": "七年級國文 - 南一版",
        "grade": "七年級",
        "subject": "國文",
        "version": "南一",
        "statistics": {
          "chapters": 12,
          "units": 48,
          "videos": 156,
          "exercises": 324
        },
        "last_updated": "2024-03-15T10:30:00Z"
      }
    ]
  }
}
```

### 6.6 匯出學校配置
**接口**: `POST /schools/{school_id}/export-config`

**描述**: 匯出學校配置檔案

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| school_id | string | 是 | 學校ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| download_url | string | 下載URL |
| expires_at | string | 過期時間 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "download_url": "https://api.studyplan.com/downloads/config_school_001.json",
    "expires_at": "2024-01-16T10:30:00Z"
  }
}
```

### 6.7 匯入學校配置
**接口**: `POST /schools/{school_id}/import-config`

**描述**: 匯入學校配置檔案

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| school_id | string | 是 | 學校ID |

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| config_file | string | 是 | Base64編碼的配置檔案內容 |
| overwrite_existing | boolean | 否 | 是否覆蓋現有配置 |

**請求體**:
```json
{
  "config_file": "base64_encoded_config_content",
  "overwrite_existing": false
}
```

---

## 7. 通用功能 API

### 7.1 獲取年級列表
**接口**: `GET /common/grades`

**描述**: 獲取系統支援的年級列表

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| grades | array | 年級列表 |
| └─ id | string | 年級ID |
| └─ name | string | 年級名稱 |
| └─ level | integer | 年級等級 |
| └─ school_type | string | 學校類型 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "grades": [
      {
        "id": "grade_001",
        "name": "七年級",
        "level": 7,
        "school_type": "國中"
      },
      {
        "id": "grade_002", 
        "name": "八年級",
        "level": 8,
        "school_type": "國中"
      }
    ]
  }
}
```

### 7.2 獲取科目列表
**接口**: `GET /common/subjects`

**描述**: 獲取系統支援的科目列表

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| subjects | array | 科目列表 |
| └─ id | string | 科目ID |
| └─ name | string | 科目名稱 |
| └─ code | string | 科目代碼 |
| └─ color | string | 科目顏色 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "subjects": [
      {
        "id": "subject_001",
        "name": "國文",
        "code": "chinese",
        "color": "#ff4444"
      },
      {
        "id": "subject_002",
        "name": "英文", 
        "code": "english",
        "color": "#44ff44"
      }
    ]
  }
}
```

### 7.3 獲取班級列表
**接口**: `GET /common/classes`

**描述**: 獲取班級列表

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| grade | string | 否 | 年級篩選 |
| school_id | string | 否 | 學校ID篩選 |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| classes | array | 班級列表 |
| └─ id | string | 班級ID |
| └─ name | string | 班級名稱 |
| └─ grade | string | 年級 |
| └─ school_id | string | 學校ID |
| └─ student_count | integer | 學生數量 |

### 7.4 檔案上傳
**接口**: `POST /common/upload`

**描述**: 上傳檔案

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| file | file | 是 | 要上傳的檔案 |
| type | string | 是 | 檔案類型 |

**參數選項**:
- `type`: avatar(頭像) / document(文件) / image(圖片)

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| file_id | string | 檔案ID |
| file_name | string | 檔案名稱 |
| file_size | integer | 檔案大小(位元組) |
| file_url | string | 檔案URL |
| mime_type | string | MIME類型 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "file_id": "file_001",
    "file_name": "document.pdf",
    "file_size": 1024000,
    "file_url": "https://cdn.studyplan.com/files/document.pdf",
    "mime_type": "application/pdf"
  }
}
```

### 7.5 獲取通知列表
**接口**: `GET /common/notifications`

**描述**: 獲取系統通知

**請求參數**:

| 參數名 | 類型 | 必填 | 說明 | 預設值 |
|--------|------|------|------|--------|
| page | integer | 否 | 頁碼 | 1 |
| limit | integer | 否 | 每頁數量 | 20 |
| unread_only | boolean | 否 | 只顯示未讀通知 | false |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| notifications | array | 通知列表 |
| └─ id | string | 通知ID |
| └─ title | string | 通知標題 |
| └─ message | string | 通知內容 |
| └─ type | string | 通知類型 |
| └─ is_read | boolean | 是否已讀 |
| └─ created_at | string | 創建時間 |
| unread_count | integer | 未讀通知數量 |

**響應示例**:
```json
{
  "success": true,
  "code": 200,
  "data": {
    "notifications": [
      {
        "id": "notification_001",
        "title": "考試提醒",
        "message": "數學期中考試將於3天後舉行",
        "type": "exam_reminder",
        "is_read": false,
        "created_at": "2024-01-15T10:30:00Z"
      }
    ],
    "unread_count": 5
  }
}
```

### 7.6 標記通知為已讀
**接口**: `PUT /common/notifications/{notification_id}/read`

**描述**: 標記通知為已讀

**路徑參數**:

| 參數名 | 類型 | 必填 | 說明 |
|--------|------|------|------|
| notification_id | string | 是 | 通知ID |

**響應參數**:

| 參數名 | 類型 | 說明 |
|--------|------|------|
| success | boolean | 操作是否成功 |
| message | string | 操作結果訊息 |

---

## 8. 錯誤代碼說明

### 通用錯誤代碼
- `200`: 成功
- `201`: 創建成功
- `400`: 請求參數錯誤
- `401`: 未授權
- `403`: 權限不足
- `404`: 資源不存在
- `409`: 資源衝突
- `422`: 驗證失敗
- `500`: 伺服器內部錯誤

### 業務錯誤代碼
- `1001`: 學生不存在
- `1002`: 學習計劃不存在
- `1003`: 考試不存在
- `1004`: 學校不存在
- `1005`: 班級不存在
- `1006`: 科目不存在
- `1007`: 檔案上傳失敗
- `1008`: 權限不足
- `1009`: 資源已被使用
- `1010`: 操作頻率過高

---

### 5.5 管理課外活動

#### 5.5.1 新增課外活動
**POST** `/profile/extracurricular-activities`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| name | string | 是 | 活動名稱 | "數學補習班" |
| schedule.days | array | 是 | 活動日期 (monday, tuesday, wednesday, thursday, friday, saturday, sunday) | ["tuesday", "thursday"] |
| schedule.time | string | 是 | 活動時間 (HH:MM-HH:MM) | "19:00-21:00" |

#### 請求範例
```json
{
  "name": "數學補習班",
  "schedule": {
    "days": ["tuesday", "thursday"],
    "time": "19:00-21:00"
  }
}
```

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.id | string | 活動ID | "activity_123" |
| data.created_at | string | 創建時間 | "2024-01-15T10:30:00Z" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "id": "activity_123",
    "created_at": "2024-01-15T10:30:00Z"
  }
}
```

#### 5.5.2 刪除課外活動
**DELETE** `/profile/extracurricular-activities/{activity_id}`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 路徑參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| activity_id | string | 是 | 活動ID | "activity_123" |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| message | string | 操作結果訊息 | "活動已刪除" |

#### 響應範例
```json
{
  "success": true,
  "message": "活動已刪除"
}
```

### 5.6 管理特殊日期

#### 5.6.1 新增特殊日期
**POST** `/profile/special-dates`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 請求參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| title | string | 是 | 特殊日期標題 | "家庭旅行" |
| start_date | string | 是 | 開始日期 (YYYY-MM-DD) | "2024-05-20" |
| end_date | string | 是 | 結束日期 (YYYY-MM-DD) | "2024-05-25" |

#### 請求範例
```json
{
  "title": "家庭旅行",
  "start_date": "2024-05-20",
  "end_date": "2024-05-25"
}
```

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| data.id | string | 特殊日期ID | "special_123" |
| data.created_at | string | 創建時間 | "2024-01-15T10:30:00Z" |

#### 響應範例
```json
{
  "success": true,
  "data": {
    "id": "special_123",
    "created_at": "2024-01-15T10:30:00Z"
  }
}
```

#### 5.6.2 刪除特殊日期
**DELETE** `/profile/special-dates/{special_date_id}`

#### 請求頭

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| Authorization | string | 是 | Bearer Token認證 | "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." |

#### 路徑參數

| 參數名 | 類型 | 必填 | 描述 | 範例 |
|--------|------|------|------|------|
| special_date_id | string | 是 | 特殊日期ID | "special_123" |

#### 響應參數

| 參數名 | 類型 | 描述 | 範例 |
|--------|------|------|------|
| success | boolean | 請求是否成功 | true |
| message | string | 操作結果訊息 | "特殊日期已刪除" |

#### 響應範例
```json
{
  "success": true,
  "message": "特殊日期已刪除"
}
```

POST last-subject-scores 創建近期考試成績紀錄
data: {
  date: "2024-05-20",
  scores: [
    {
      subject: "數學",
      score: 85,
    },
    {
      subject: "英文",
      score: 90,
    },
    ...
  ]
}


PUT last-subject-grade 更新近期考試成績紀錄
data: {
  date: "2024-05-20",
  scores: [
    {
      subject: "數學",
      score: 85,
    },
    {
      subject: "英文",
      score: 90,
    },
    ...
  ]
}

GET last-subject-grades 獲取近期考試成績紀錄
data: [
  {
    date: "2024-05-20",
  scores: [
    {
      subject: "數學",
      score: 85,
    },
    {
      subject: "英文",
      score: 90,
    },
    ...
  ]
  }
]

