# eav-chat-voice-cms
CMS sử dụng voice &amp; chat để chăm sóc khách hàng
# Cấu trúc dữ liệu:
1. EntityType (hoặc Schema, EntitySchema)

Mục đích: Xác định loại thực thể (sản phẩm, bài viết, bệnh nhân, v.v.)

EntityType
-------------------------
id            PK
name          TEXT         -- Tên loại entity: "Sản phẩm", "Bệnh nhân"
description   TEXT

🧱 2. Entity

Mục đích: Đại diện cho một đối tượng cụ thể, có thể là sản phẩm, bài viết, người, v.v.

Entity
-------------------------
id            PK
entity_type_id   FK → EntityType.id
name          TEXT
created_at    DATETIME
updated_at    DATETIME

🧱 3. AttributeGroup (tuỳ chọn nhưng rất hữu ích)

Mục đích: Gom nhóm các thuộc tính (ví dụ: "Thông tin cơ bản", "Chi tiết kỹ thuật").

AttributeGroup
-------------------------
id            PK
name          TEXT
entity_type_id   FK → EntityType.id
order         INTEGER

🧱 4. Attribute

Mục đích: Xác định thuộc tính cụ thể (màu sắc, kích thước, thương hiệu…)

Attribute
-------------------------
id             PK
name           TEXT             -- Ví dụ: "Màu sắc"
code           TEXT UNIQUE      -- slug: "color"
data_type      TEXT             -- string | number | date | boolean | enum | reference
unit           TEXT             -- Tuỳ chọn: "kg", "cm"
is_required    BOOLEAN          -- Có bắt buộc nhập không
attribute_group_id FK → AttributeGroup.id (nullable)

🧱 5. EntityAttributeValue

Mục đích: Bảng chứa giá trị thật của mỗi entity–attribute

EntityAttributeValue
-------------------------
id              PK
entity_id       FK → Entity.id
attribute_id    FK → Attribute.id
value_string    TEXT
value_number    DECIMAL
value_date      DATE
value_boolean   BOOLEAN
value_enum      TEXT
value_reference INTEGER   -- FK tới entity khác (nếu type là reference)

⚙️ Cách chọn trường "value_*" phù hợp:

    Khi attribute là "string" → điền vào value_string

    Khi attribute là "number" → điền vào value_number

    Khi là "boolean" → dùng value_boolean
    ...

    ...

🧠 Gợi ý thêm: EnumOption (cho attribute dạng enum)

EnumOption
-------------------------
id              PK
attribute_id    FK → Attribute.id
value           TEXT
label           TEXT      -- Hiển thị người dùng
order           INTEGER

📊 Tổng kết sơ đồ quan hệ

[EntityType]──┐
              └──<Entity>──<EntityAttributeValue>──<Attribute>──<AttributeGroup>──<EntityType>
                                                        │
                                                [EnumOption]
