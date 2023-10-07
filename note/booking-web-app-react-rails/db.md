## 2. PostgreSQLデータベース設計

### 2.1 Usersテーブル

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    admin BOOLEAN DEFAULT FALSE NOT NULL,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

### 2.2 Roomsテーブル

```sql
CREATE TABLE rooms (
    id SERIAL PRIMARY KEY,
    room_number VARCHAR(255) NOT NULL UNIQUE,
    description TEXT,
    capacity INTEGER NOT NULL,
    price_per_night DECIMAL NOT NULL,
    room_type VARCHAR(255) NOT NULL,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

### 2.3 Bookingsテーブル

```sql
CREATE TABLE bookings (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE NOT NULL,
    room_id INTEGER REFERENCES rooms(id) ON DELETE CASCADE NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    total_price DECIMAL NOT NULL,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

## 3. Railsのマイグレーション

### 3.1 Usersテーブル

```ruby
create_table :rooms do |t|
  t.string :room_number, null: false, unique: true
  t.text :description
  t.integer :capacity, null: false
  t.decimal :price_per_night, null: false
  t.string :room_type, null: false
  t.timestamps
end
```

### 3.2 Roomsテーブル

```ruby
create_table :rooms do |t|
  t.string :room_number, null: false, unique: true
  t.text :description
  t.integer :capacity, null: false
  t.decimal :price_per_night, null: false
  t.string :room_type, null: false
  t.timestamps
end
```

### 3.3 Bookingsテーブル

```ruby
create_table :bookings do |t|
  t.references :user, foreign_key: true, null: false
  t.references :room, foreign_key: true, null: false
  t.date :start_date, null: false
  t.date :end_date, null: false
  t.decimal :total_price, null: false
  t.timestamps
end
```