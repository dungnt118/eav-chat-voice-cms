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
**Ứng dụng
- Lưu trữ mọi loại thông tin phổ thông để tra cứu như:
* Sản phẩm:
     + Các loại sản phẩm, thuộc tính sản phẩm, chỉ dẫn
* FAQ
     + Các tình huống thực tế, khó khăn và cách giải quyết
* Kho tri thức (Knowledge)
  
=> Có thể lưu trữ như các đài liệu đào tạo để huấn luyện hoặc nhờ AI tra cứu và chăm sóc trả lời khách hàng, cũng như sử dụng làm trợ lý để hỗ trợ công việc.

**Các chức năng cụ thể
1. Kho tri thức (có thể lưu thông tin sản phẩm, bộ FAQ, quản lý theo từng Schema (đóng vai trò như 1 mục đích sử dụng khác nhau - tương đương như các Table của larksuit - có khả năng tùy biến rất cao)
2. Quản lý Calendar (tích hợp với google calendar để lên lịch) => cái này sẽ sử dụng dữ liệu có cấu trúc để tiện đồng bộ lịch cá nhân ngươi dùng với google
3. Tích hợp Chatbox kết hơp toolcall của openai để hỗ trợ người dùng
4. Tích hợp Voice Chat => là trợ lý sử dụng giọng nói để hỗ trợ người dùng
5. Quản lý Agent (lưu trữ các prompt hệ thống và phạm vi truy xuất thông tin tới các Schema nào để tìm kiếm thông tin - thông qua cơ chế toolcall)
