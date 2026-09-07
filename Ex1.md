Việc dùng Spring Cloud BOM (Bill of Materials) giúp đồng bộ hóa toàn bộ phiên bản thư viện Spring Cloud một cách tập trung, ngăn chặn xung đột phiên bản và lỗi build khi hệ thống mở rộng nhiều service.
Dưới đây là lời giải chi tiết cho bài tập chuẩn hóa dependency Spring Cloud:
1. Phân tích vấn đề khai báo version cố định, rời rạc
   ⚬	Xung đột thư viện (Version Mismatch): Các module Spring Cloud phụ thuộc lẫn nhau và vào các thư viện bên dưới (như Spring Framework, Netty). Việc ép version độc lập cho từng starter dễ dẫn đến lệch pha và lỗi xung đột lúc build hoặc runtime.
   ⚬	Khó khăn trong bảo trì: Khi hệ thống mở rộng thêm nhiều service, việc phải thay đổi thủ công từng version ở từng file cấu hình của từng service rất dễ gây sót, dẫn đến hệ thống chạy lệch phiên bản giữa các thành phần.
2. File build.gradle chuẩn sau khi sửa (Áp dụng cho cả 3 service)
```java
    plugins {
       id 'org.springframework.boot' version '3.1.5'
       id 'io.spring.dependency-management' version '1.1.3'
       id 'java'
       }
    
    group = 'com.foodx'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '17'
    
    repositories {
    mavenCentral()
    }
    
    ext {
    set('springCloudVersion', "2022.0.4") // Phiên bản Spring Cloud tương thích với Spring Boot 3.1.x
    }
    
    dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    // Bỏ khai báo version cố định ở từng dependency
    implementation 'org.springframework.cloud:spring-cloud-starter-config'
    implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
    }
    
    dependencyManagement {
    imports {
    mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
    }
```

3. Nội dung README giải thích vai trò của Spring Cloud BOM
   ⚬	Quản lý phiên bản tập trung: Spring Cloud BOM đóng vai trò như một "bản kê phiên bản", tự động gán phiên bản chuẩn và tương thích nhất cho tất cả các starter mà không cần ghi đè thủ công.
   ⚬	Đảm bảo tính đồng bộ hệ thống: Giúp restaurant-service, order-service và delivery-service dùng chung một bộ thư viện nền tảng, loại bỏ hoàn toàn rủi ro xung đột trước khi bước sang các bài tích hợp Config Server và Eureka Server.