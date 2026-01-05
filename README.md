# Video Generation Platform - System Architecture
maid
graph TB
    subgraph Frontend["Frontend Layer"]
        React["React.js Application<br/>Port: 3000<br/>- User Interface<br/>- Video Management Dashboard<br/>- Asset Upload"]
    end

    subgraph Gateway["API Gateway"]
        APIGW["Spring Cloud Gateway<br/>- Authentication/Authorization<br/>- Request Routing<br/>- Rate Limiting"]
    end

    subgraph Microservices["Microservices Layer"]
        VideoSvc["Video Generation Service<br/>Port: 8081"]
        AssetSvc["Asset Management Service<br/>Port: 8082"]
        UserSvc["User Management Service<br/>Port: 8083"]
    end

    subgraph Queue["Message Queue"]
        RabbitMQ["RabbitMQ<br/>- Async Job Processing"]
    end

    subgraph External["External Services"]
        Veo3API["Veo3/Gemini API"]
        Storage["Cloud Storage<br/>S3/Cloudinary"]
    end

    React -->|HTTP/REST| APIGW
    APIGW -->|Route| VideoSvc
    APIGW -->|Route| AssetSvc
    APIGW -->|Route| UserSvc
    VideoSvc --> RabbitMQ
    RabbitMQ --> Veo3API
    AssetSvc --> Storage
    VideoSvc --> Storage

    style Frontend fill:#e1f5ff
    style Gateway fill:#fff4e1
    style Microservices fill:#e8f5e9
    style Queue fill:#f3e5f5
    style External fill:#ffebee## 2. PlantUML Diagram (cho các tool như PlantUML Server, VS Code extension)

Tạo file `architecture-diagram.puml`:

@startuml Video Generation Platform Architecture

!theme plain
skinparam componentStyle rectangle
skinparam backgroundColor #FFFFFF

package "Frontend Layer" {
  [React.js Application\nPort: 3000\n- User Interface\n- Video Management Dashboard\n- Asset Upload] as Frontend
}

package "API Gateway" {
  [Spring Cloud Gateway\n- Authentication/Authorization\n- Request Routing\n- Rate Limiting] as Gateway
}

package "Microservices Layer" {
  [Video Generation Service\nPort: 8081] as VideoService
  [Asset Management Service\nPort: 8082] as AssetService
  [User Management Service\nPort: 8083] as UserService
}

package "Message Queue" {
  [RabbitMQ\n- Async Job Processing] as RabbitMQ
}

cloud "External APIs" {
  [Veo3/Gemini API] as Veo3API
  [Cloud Storage\nS3/Cloudinary] as Storage
}

Frontend -down-> Gateway : HTTP/REST
Gateway -down-> VideoService : Route
Gateway -down-> AssetService : Route
Gateway -down-> UserService : Route
VideoService -down-> RabbitMQ : Publish Jobs
RabbitMQ -down-> Veo3API : Process Video
AssetService -down-> Storage : Upload Assets
VideoService -down-> Storage : Store Videos

@enduml## 3. Mermaid Sequence Diagram (Flow tạo video)

Tạo file `video-generation-flow.md`:
rkdown
# Video Generation Flow
id
sequenceDiagram
    participant User as User (Frontend)
    participant Gateway as API Gateway
    participant UserSvc as User Service
    participant VideoSvc as Video Generation Service
    participant AssetSvc as Asset Service
    participant Queue as RabbitMQ
    participant Worker as Video Worker
    participant Veo3 as Veo3 API
    participant Storage as Cloud Storage

    User->>Gateway: POST /api/v1/video/generate
    Gateway->>UserSvc: Validate Token
    UserSvc-->>Gateway: User Valid
    Gateway->>AssetSvc: Get Asset URLs
    AssetSvc-->>Gateway: Asset URLs
    Gateway->>VideoSvc: Create Video Job
    VideoSvc->>Queue: Publish Job
    VideoSvc-->>Gateway: Job ID (202 Accepted)
    Gateway-->>User: Return Job ID
    
    Queue->>Worker: Consume Job
    Worker->>Veo3: Request Video Generation
    Veo3-->>Worker: Job ID (Processing)
    
    loop Poll Status
        Worker->>Veo3: Check Status
        Veo3-->>Worker: Status: Processing
    end
    
    Veo3-->>Worker: Status: Completed + Video URL
    Worker->>Storage: Download & Upload Video
    Worker->>VideoSvc: Update Job Status
    VideoSvc-->>User: WebSocket Notification
    User->>Gateway: GET /api/v1/video/status/{jobId}
    Gateway->>VideoSvc: Get Status
    VideoSvc-->>Gateway: Video Ready
    Gateway-->>User: Video URL
## 4. HTML file với Mermaid (có thể mở trực tiếp)

Tạo file `architecture-viewer.html`:
tml
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Video Generation Platform - Architecture Diagram</title>
    <script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #333;
            text-align: center;
            margin-bottom: 30px;
        }
        .diagram-container {
            margin: 30px 0;
        }
        .export-buttons {
            text-align: center;
            margin: 20px 0;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin: 5px;
            font-size: 14px;
        }
        button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Video Generation Platform - System Architecture</h1>
        
        <div class="export-buttons">
            <button onclick="exportAsPNG()">Export as PNG</button>
            <button onclick="exportAsSVG()">Export as SVG</button>
        </div>

        <div class="diagram-container">
            <div class="mermaid">
graph TB
    subgraph Frontend["Frontend Layer"]
        React["React.js Application<br/>Port: 3000<br/>- User Interface<br/>- Video Management Dashboard<br/>- Asset Upload"]
    end

    subgraph Gateway["API Gateway"]
        APIGW["Spring Cloud Gateway<br/>- Authentication/Authorization<br/>- Request Routing<br/>- Rate Limiting"]
    end

    subgraph Microservices["Microservices Layer"]
        VideoSvc["Video Generation Service<br/>Port: 8081"]
        AssetSvc["Asset Management Service<br/>Port: 8082"]
        UserSvc["User Management Service<br/>Port: 8083"]
    end

    subgraph Queue["Message Queue"]
        RabbitMQ["RabbitMQ<br/>- Async Job Processing"]
    end

    subgraph External["External Services"]
        Veo3API["Veo3/Gemini API"]
        Storage["Cloud Storage<br/>S3/Cloudinary"]
    end

    React -->|HTTP/REST| APIGW
    APIGW -->|Route| VideoSvc
    APIGW -->|Route| AssetSvc
    APIGW -->|Route| UserSvc
    VideoSvc --> RabbitMQ
    RabbitMQ --> Veo3API
    AssetSvc --> Storage
    VideoSvc --> Storage

    style Frontend fill:#e1f5ff,stroke:#01579b,stroke-width:2px
    style Gateway fill:#fff4e1,stroke:#e65100,stroke-width:2px
    style Microservices fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px
    style Queue fill:#f3e5f5,stroke:#6a1b9a,stroke-width:2px
    style External fill:#ffebee,stroke:#c62828,stroke-width:2px
            </div>
        </div>

        <h2 style="margin-top: 50px;">Video Generation Flow</h2>
        <div class="diagram-container">
            <div class="mermaid">
sequenceDiagram
    participant User as User (Frontend)
    participant Gateway as API Gateway
    participant UserSvc as User Service
    participant VideoSvc as Video Generation Service
    participant AssetSvc as Asset Service
    participant Queue as RabbitMQ
    participant Worker as Video Worker
    participant Veo3 as Veo3 API
    participant Storage as Cloud Storage

    User->>Gateway: POST /api/v1/video/generate
    Gateway->>UserSvc: Validate Token
    UserSvc-->>Gateway: User Valid
    Gateway->>AssetSvc: Get Asset URLs
    AssetSvc-->>Gateway: Asset URLs
    Gateway->>VideoSvc: Create Video Job
    VideoSvc->>Queue: Publish Job
    VideoSvc-->>Gateway: Job ID (202 Accepted)
    Gateway-->>User: Return Job ID
    
    Queue->>Worker: Consume Job
    Worker->>Veo3: Request Video Generation
    Veo3-->>Worker: Job ID (Processing)
    
    loop Poll Status
        Worker->>Veo3: Check Status
        Veo3-->>Worker: Status: Processing
    end
    
    Veo3-->>Worker: Status: Completed + Video URL
    Worker->>Storage: Download & Upload Video
    Worker->>VideoSvc: Update Job Status
    VideoSvc-->>User: WebSocket Notification
            </div>
        </div>
    </div>

    <script>
        mermaid.initialize({ 
            startOnLoad: true,
            theme: 'default',
            flowchart: {
                useMaxWidth: true,
                htmlLabels: true,
                curve: 'basis'
            }
        });

        function exportAsPNG() {
            alert('Để export PNG, bạn có thể:\n1. Mở file này trong browser\n2. Right-click vào diagram\n3. Chọn "Save image as..."\nHoặc sử dụng Mermaid CLI: mmdc -i architecture-viewer.html -o diagram.png');
        }

        function exportAsSVG() {
            alert('Để export SVG, bạn có thể:\n1. Inspect element và tìm thẻ SVG\n2. Copy SVG code\n3. Paste vào file .svg\nHoặc sử dụng Mermaid Live Editor: https://mermaid.live');
        }
    </script>
</body>
</html>
## Cách sử dụng để tạo hình ảnh

### Cách 1: Mermaid Live Editor (dễ nhất)
1. Vào https://mermaid.live
2. Copy code Mermaid ở trên
3. Paste vào editor
4. Click "Actions" → "Download PNG/SVG"

### Cách 2: VS Code Extension
1. Cài extension "Markdown Preview Mermaid Support"
2. Tạo file `.md` với code Mermaid
3. Preview và export

### Cách 3: PlantUML
1. Cài PlantUML extension trong VS Code
2. Mở file `.puml`
3. Preview và export PNG/SVG

### Cách 4: Mermaid CLIh
npm install -g @mermaid-js/mermaid-cli
mmdc -i architecture-diagram.md -o architecture.png## Tóm tắt files cần tạo

1. `architecture-diagram.md` - Mermaid diagram chính
2. `video-generation-flow.md` - Sequence diagram cho flow
3. `architecture-diagram.puml` - PlantUML version
4. `architecture-viewer.html` - HTML viewer có thể mở trực tiếp

Bạn muốn tôi tạo file nào trước, hoặc cần thêm loại diagram nào khác (component diagram, deployment diagram, v.v.)?
# Video Generation Platform - System Architecture
maid
graph TB
    subgraph Frontend["Frontend Layer"]
        React["React.js Application<br/>Port: 3000<br/>- User Interface<br/>- Video Management Dashboard<br/>- Asset Upload"]
    end

    subgraph Gateway["API Gateway"]
        APIGW["Spring Cloud Gateway<br/>- Authentication/Authorization<br/>- Request Routing<br/>- Rate Limiting"]
    end

    subgraph Microservices["Microservices Layer"]
        VideoSvc["Video Generation Service<br/>Port: 8081"]
        AssetSvc["Asset Management Service<br/>Port: 8082"]
        UserSvc["User Management Service<br/>Port: 8083"]
    end

    subgraph Queue["Message Queue"]
        RabbitMQ["RabbitMQ<br/>- Async Job Processing"]
    end

    subgraph External["External Services"]
        Veo3API["Veo3/Gemini API"]
        Storage["Cloud Storage<br/>S3/Cloudinary"]
    end

    React -->|HTTP/REST| APIGW
    APIGW -->|Route| VideoSvc
    APIGW -->|Route| AssetSvc
    APIGW -->|Route| UserSvc
    VideoSvc --> RabbitMQ
    RabbitMQ --> Veo3API
    AssetSvc --> Storage
    VideoSvc --> Storage

    style Frontend fill:#e1f5ff
    style Gateway fill:#fff4e1
    style Microservices fill:#e8f5e9
    style Queue fill:#f3e5f5
    style External fill:#ffebee## 2. PlantUML Diagram (cho các tool như PlantUML Server, VS Code extension)

Tạo file `architecture-diagram.puml`:

@startuml Video Generation Platform Architecture

!theme plain
skinparam componentStyle rectangle
skinparam backgroundColor #FFFFFF

package "Frontend Layer" {
  [React.js Application\nPort: 3000\n- User Interface\n- Video Management Dashboard\n- Asset Upload] as Frontend
}

package "API Gateway" {
  [Spring Cloud Gateway\n- Authentication/Authorization\n- Request Routing\n- Rate Limiting] as Gateway
}

package "Microservices Layer" {
  [Video Generation Service\nPort: 8081] as VideoService
  [Asset Management Service\nPort: 8082] as AssetService
  [User Management Service\nPort: 8083] as UserService
}

package "Message Queue" {
  [RabbitMQ\n- Async Job Processing] as RabbitMQ
}

cloud "External APIs" {
  [Veo3/Gemini API] as Veo3API
  [Cloud Storage\nS3/Cloudinary] as Storage
}

Frontend -down-> Gateway : HTTP/REST
Gateway -down-> VideoService : Route
Gateway -down-> AssetService : Route
Gateway -down-> UserService : Route
VideoService -down-> RabbitMQ : Publish Jobs
RabbitMQ -down-> Veo3API : Process Video
AssetService -down-> Storage : Upload Assets
VideoService -down-> Storage : Store Videos

@enduml## 3. Mermaid Sequence Diagram (Flow tạo video)

Tạo file `video-generation-flow.md`:
rkdown
# Video Generation Flow
id
sequenceDiagram
    participant User as User (Frontend)
    participant Gateway as API Gateway
    participant UserSvc as User Service
    participant VideoSvc as Video Generation Service
    participant AssetSvc as Asset Service
    participant Queue as RabbitMQ
    participant Worker as Video Worker
    participant Veo3 as Veo3 API
    participant Storage as Cloud Storage

    User->>Gateway: POST /api/v1/video/generate
    Gateway->>UserSvc: Validate Token
    UserSvc-->>Gateway: User Valid
    Gateway->>AssetSvc: Get Asset URLs
    AssetSvc-->>Gateway: Asset URLs
    Gateway->>VideoSvc: Create Video Job
    VideoSvc->>Queue: Publish Job
    VideoSvc-->>Gateway: Job ID (202 Accepted)
    Gateway-->>User: Return Job ID
    
    Queue->>Worker: Consume Job
    Worker->>Veo3: Request Video Generation
    Veo3-->>Worker: Job ID (Processing)
    
    loop Poll Status
        Worker->>Veo3: Check Status
        Veo3-->>Worker: Status: Processing
    end
    
    Veo3-->>Worker: Status: Completed + Video URL
    Worker->>Storage: Download & Upload Video
    Worker->>VideoSvc: Update Job Status
    VideoSvc-->>User: WebSocket Notification
    User->>Gateway: GET /api/v1/video/status/{jobId}
    Gateway->>VideoSvc: Get Status
    VideoSvc-->>Gateway: Video Ready
    Gateway-->>User: Video URL
## 4. HTML file với Mermaid (có thể mở trực tiếp)

Tạo file `architecture-viewer.html`:
tml
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Video Generation Platform - Architecture Diagram</title>
    <script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #333;
            text-align: center;
            margin-bottom: 30px;
        }
        .diagram-container {
            margin: 30px 0;
        }
        .export-buttons {
            text-align: center;
            margin: 20px 0;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin: 5px;
            font-size: 14px;
        }
        button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Video Generation Platform - System Architecture</h1>
        
        <div class="export-buttons">
            <button onclick="exportAsPNG()">Export as PNG</button>
            <button onclick="exportAsSVG()">Export as SVG</button>
        </div>

        <div class="diagram-container">
            <div class="mermaid">
graph TB
    subgraph Frontend["Frontend Layer"]
        React["React.js Application<br/>Port: 3000<br/>- User Interface<br/>- Video Management Dashboard<br/>- Asset Upload"]
    end

    subgraph Gateway["API Gateway"]
        APIGW["Spring Cloud Gateway<br/>- Authentication/Authorization<br/>- Request Routing<br/>- Rate Limiting"]
    end

    subgraph Microservices["Microservices Layer"]
        VideoSvc["Video Generation Service<br/>Port: 8081"]
        AssetSvc["Asset Management Service<br/>Port: 8082"]
        UserSvc["User Management Service<br/>Port: 8083"]
    end

    subgraph Queue["Message Queue"]
        RabbitMQ["RabbitMQ<br/>- Async Job Processing"]
    end

    subgraph External["External Services"]
        Veo3API["Veo3/Gemini API"]
        Storage["Cloud Storage<br/>S3/Cloudinary"]
    end

    React -->|HTTP/REST| APIGW
    APIGW -->|Route| VideoSvc
    APIGW -->|Route| AssetSvc
    APIGW -->|Route| UserSvc
    VideoSvc --> RabbitMQ
    RabbitMQ --> Veo3API
    AssetSvc --> Storage
    VideoSvc --> Storage

    style Frontend fill:#e1f5ff,stroke:#01579b,stroke-width:2px
    style Gateway fill:#fff4e1,stroke:#e65100,stroke-width:2px
    style Microservices fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px
    style Queue fill:#f3e5f5,stroke:#6a1b9a,stroke-width:2px
    style External fill:#ffebee,stroke:#c62828,stroke-width:2px
            </div>
        </div>

        <h2 style="margin-top: 50px;">Video Generation Flow</h2>
        <div class="diagram-container">
            <div class="mermaid">
sequenceDiagram
    participant User as User (Frontend)
    participant Gateway as API Gateway
    participant UserSvc as User Service
    participant VideoSvc as Video Generation Service
    participant AssetSvc as Asset Service
    participant Queue as RabbitMQ
    participant Worker as Video Worker
    participant Veo3 as Veo3 API
    participant Storage as Cloud Storage

    User->>Gateway: POST /api/v1/video/generate
    Gateway->>UserSvc: Validate Token
    UserSvc-->>Gateway: User Valid
    Gateway->>AssetSvc: Get Asset URLs
    AssetSvc-->>Gateway: Asset URLs
    Gateway->>VideoSvc: Create Video Job
    VideoSvc->>Queue: Publish Job
    VideoSvc-->>Gateway: Job ID (202 Accepted)
    Gateway-->>User: Return Job ID
    
    Queue->>Worker: Consume Job
    Worker->>Veo3: Request Video Generation
    Veo3-->>Worker: Job ID (Processing)
    
    loop Poll Status
        Worker->>Veo3: Check Status
        Veo3-->>Worker: Status: Processing
    end
    
    Veo3-->>Worker: Status: Completed + Video URL
    Worker->>Storage: Download & Upload Video
    Worker->>VideoSvc: Update Job Status
    VideoSvc-->>User: WebSocket Notification
            </div>
        </div>
    </div>

    <script>
        mermaid.initialize({ 
            startOnLoad: true,
            theme: 'default',
            flowchart: {
                useMaxWidth: true,
                htmlLabels: true,
                curve: 'basis'
            }
        });

        function exportAsPNG() {
            alert('Để export PNG, bạn có thể:\n1. Mở file này trong browser\n2. Right-click vào diagram\n3. Chọn "Save image as..."\nHoặc sử dụng Mermaid CLI: mmdc -i architecture-viewer.html -o diagram.png');
        }

        function exportAsSVG() {
            alert('Để export SVG, bạn có thể:\n1. Inspect element và tìm thẻ SVG\n2. Copy SVG code\n3. Paste vào file .svg\nHoặc sử dụng Mermaid Live Editor: https://mermaid.live');
        }
    </script>
</body>
</html>
## Cách sử dụng để tạo hình ảnh

### Cách 1: Mermaid Live Editor (dễ nhất)
1. Vào https://mermaid.live
2. Copy code Mermaid ở trên
3. Paste vào editor
4. Click "Actions" → "Download PNG/SVG"

### Cách 2: VS Code Extension
1. Cài extension "Markdown Preview Mermaid Support"
2. Tạo file `.md` với code Mermaid
3. Preview và export

### Cách 3: PlantUML
1. Cài PlantUML extension trong VS Code
2. Mở file `.puml`
3. Preview và export PNG/SVG

### Cách 4: Mermaid CLIh
npm install -g @mermaid-js/mermaid-cli
mmdc -i architecture-diagram.md -o architecture.png## Tóm tắt files cần tạo

1. `architecture-diagram.md` - Mermaid diagram chính
2. `video-generation-flow.md` - Sequence diagram cho flow
3. `architecture-diagram.puml` - PlantUML version
4. `architecture-viewer.html` - HTML viewer có thể mở trực tiếp

Bạn muốn tôi tạo file nào trước, hoặc cần thêm loại diagram nào khác (component diagram, deployment diagram, v.v.)?
