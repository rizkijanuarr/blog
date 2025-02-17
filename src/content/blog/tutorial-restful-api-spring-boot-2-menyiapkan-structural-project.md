---
title: Tutorial Restful API Spring Boot 2 || Menyiapkan Structural Project dan Utils
publishDate: 2025-02-17T04:00:07.517Z
draft: false
tags: []
---

Pada seri artikel ini mungkin Anda bertanya-tanya mengapa kita harus menyiapkan dari awal perihal structural project? Pertanyaan yang menarik, mungkin untuk project skala kecil yang mana hanya di maintaince oleh satu orang mungkin kita gak perlu menerapkan ini, akan tetapi saya akan mencontohkan bagaimana ketika project sudah skala besar dan yang maintaince bukan hanya satu orang saja. 

## ✨ Langkah 1 - Menata Folder 

Pertama kali, saya akan menunjukan Anda bagaimana structural project yang akan digunakan nantinya. Silahkan buat beberapa folder seperti berikut.

```bash
📂 src
 ├── 📂 main
 │    ├── 📂 java
 │    │    ├── 📂 com.xxx.spring_boot_rest_api
 │    │    │    ├── 📂 controller
 │    │    │    │    ├── 📂 v1
 │    │    │    │    │    ├── 📂 impl
 │    │    │    ├── 📂 repositories
 │    │    │    │    ├── 📂 entities
 │    │    │    │    ├── 📂 enumeration
 │    │    │    ├── 📂 request
 │    │    │    │    ├── 📂 v1
 │    │    │    │    │    ├── 📂 impl
 │    │    │    ├── 📂 response
 │    │    │    │    ├── 📂 v1
 │    │    │    │    │    ├── 📂 impl
 │    │    │    ├── 📂 services
 │    │    │    │    ├── 📂 v1
 │    │    │    │    │    ├── 📂 impl
 │    │    │    ├── 📂 utils
 │    │    │    │    ├── 📂 date
 │    │    │    │    ├── 📂 response
 │    │    │    ├── 📄 SpringBootRestApiApplication
 │    ├── 📂 resources
 │    │    ├── 📂 static.uploads
 │    │    ├── 📂 templates
 │    │    ├── 📄 application.properties
```

## 👨🏻‍💻 Langkah 2 - Menyiapkan Utils

Setelah itu mari kita siapkan beberapa utils, mulai dari menyiapkan ApiResponse yang diinginkan dan menyiapkan format date yang diinginkan

- **Step 1 - Lakukan pembuatan ApiResponse**

Pertama sekali, silahkan membuat file **ApiResponse.java** pada folder 📂 response, kemudian isikan kodenya menjadi seperti berikut ini.

```bash
public class ApiResponse<T> {

    private boolean success;
    private String message;
    private T data;
    
    public ApiResponse(boolean success, String message, T data) {
        this.success = success;
        this.message = message;
        this.data = data;
    }
    
    public boolean isSuccess() {
        return success;
    }

    public void setSuccess(boolean success) {
        this.success = success;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```

- **Step 2 - Lakukan pembuatan Date**

Setelah itu, silahkan membuat **Date.java** pada folder 📂 date, kemudian isikan kodenya menjadi seperti berikut ini.

```bash
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Locale;

public class Date {

    // ISO 8601 (Default JSON API)
    private static final DateTimeFormatter ISO_FORMATTER =
            DateTimeFormatter.ofPattern("yyyy-MM-dd'T'HH:mm:ss.SSSXXX");

    // Format "10/09/2024"
    private static final DateTimeFormatter SLASH_FORMATTER =
            DateTimeFormatter.ofPattern("dd/MM/yyyy");

    // Format "10-09-2024"
    private static final DateTimeFormatter DASH_FORMATTER =
            DateTimeFormatter.ofPattern("dd-MM-yyyy");

    // Format "10 Sept 2024"
    private static final DateTimeFormatter SHORT_MONTH_FORMATTER =
            DateTimeFormatter.ofPattern("dd MMM yyyy", new Locale("id", "ID"));

    // Format "Selasa, 10 September 2024"
    private static final DateTimeFormatter FULL_DAY_FORMATTER =
            DateTimeFormatter.ofPattern("EEEE, dd MMMM yyyy", new Locale("id", "ID"));

    // Format "14:30:45"
    private static final DateTimeFormatter TIME_FORMATTER =
            DateTimeFormatter.ofPattern("HH:mm:ss");

    // Format "10 Sept 2024 14:30"
    private static final DateTimeFormatter SHORT_DATETIME_FORMATTER =
            DateTimeFormatter.ofPattern("dd MMM yyyy HH:mm", new Locale("id", "ID"));

    // =================== METODE UTILITAS ===================

    public static String formatIso(LocalDateTime dateTime) {
        return dateTime != null ? dateTime.format(ISO_FORMATTER) : null;
    }

    public static String formatSlash(LocalDateTime dateTime) {
        return dateTime != null ? dateTime.format(SLASH_FORMATTER) : null;
    }

    public static String formatDash(LocalDateTime dateTime) {
        return dateTime != null ? dateTime.format(DASH_FORMATTER) : null;
    }

    public static String formatShortMonth(LocalDateTime dateTime) {
        return dateTime != null ? dateTime.format(SHORT_MONTH_FORMATTER) : null;
    }

    public static String formatFullDay(LocalDateTime dateTime) {
        return dateTime != null ? dateTime.format(FULL_DAY_FORMATTER) : null;
    }

    public static String formatTime(LocalDateTime dateTime) {
        return dateTime != null ? dateTime.format(TIME_FORMATTER) : null;
    }

    public static String formatShortDateTime(LocalDateTime dateTime) {
        return dateTime != null ? dateTime.format(SHORT_DATETIME_FORMATTER) : null;
    }
}
```

## ✨ Langkah 3 - Menyiapkan BaseEntity

Setelah itu, silahkan membuat file **BaseEntity.java** pada folder 📂 entities, kemudian isikan kodenya menjadi seperti berikut ini.

```bash
import jakarta.persistence.*;
import jakarta.validation.constraints.NotNull;
import lombok.Data;
import org.hibernate.annotations.DynamicUpdate;

import java.time.LocalDateTime;
import java.util.UUID;

@MappedSuperclass
@DynamicUpdate
@Data
public class BaseEntity {
    @Id
    @Column(name = "id")
    private String id;

    @Column(name = "active", columnDefinition = "TINYINT default 1")
    @Convert(converter = org.hibernate.type.NumericBooleanConverter.class)
    @NotNull
    private Boolean active;

    @Column(name = "created_date")
    @NotNull
    private LocalDateTime createdDate;

    @Column(name = "modified_date")
    private LocalDateTime modifiedDate;

    @Column(name = "deleted_date")
    private LocalDateTime deletedDate;

    @Column(name = "created_by")
    @NotNull
    private String createdBy;

    @Column(name = "modified_by")
    private String modifiedBy;

    @Column(name = "deleted_by")
    private String deletedBy;

    @PrePersist
    public void prePersist() {
        this.id = UUID.randomUUID().toString();
        this.active = true;
        this.createdDate = LocalDateTime.now();
        this.createdBy = "SYSTEM";
    }

    @PreUpdate
    public void preUpdate() {
        this.modifiedDate = LocalDateTime.now();
    }

    @PreRemove
    public void preRemove() {
        this.deletedDate = LocalDateTime.now();
        this.active = false;
    }
}
```

Mungkin itu saja yang dapat saya sampaikan pada sesi artikel ini, semoga tulisan ini dapat bermanfaat bagi temen-temen. Saya akan melihat Anda di artikel berikutnya!

