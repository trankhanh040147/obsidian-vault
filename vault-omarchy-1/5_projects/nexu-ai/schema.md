
```sql
-- 1. Users Table (From Google Auth)
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    google_id VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    full_name VARCHAR(255),
    avatar_url TEXT,
    role VARCHAR(50) DEFAULT 'user', -- 'user', 'admin'
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 2. Features Table (Config for your services)
CREATE TABLE features (
    id SERIAL PRIMARY KEY,
    code VARCHAR(50) UNIQUE NOT NULL, -- e.g., 'MENU_CREATOR', 'ADS_GENERATOR'
    name VARCHAR(100) NOT NULL,
    daily_limit_default INT DEFAULT 5, -- Default free limit
    is_active BOOLEAN DEFAULT TRUE
);

-- 3. Generations Table (The History & Analytics Source)
-- This is crucial for your "60-day stats" (Cost, Concurrent, Requests)
CREATE TABLE generations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    feature_id INT REFERENCES features(id),
    
    -- Inputs & Outputs (Use JSONB for flexibility across different features)
    input_payload JSONB NOT NULL,  -- e.g. {"ingredients": "beef", "style": "luxury"}
    output_result JSONB,           -- The Gemini response
    
    -- Analytics
    processing_time_ms INT,        -- How long Gemini took
    tokens_used INT,               -- To calculate cost later
    status VARCHAR(50),            -- 'PENDING', 'COMPLETED', 'FAILED'
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 4. User Feature Usage (For Rate Limiting)
-- Reset this table or specific rows daily via a Cron Job or logic
CREATE TABLE user_feature_usages (
    user_id UUID REFERENCES users(id),
    feature_id INT REFERENCES features(id),
    usage_count INT DEFAULT 0,
    last_used_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    
    PRIMARY KEY (user_id, feature_id)
);

-- 5. Feedbacks
CREATE TABLE feedbacks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    generation_id UUID REFERENCES generations(id),
    user_id UUID REFERENCES users(id),
    rating INT CHECK (rating >= 1 AND rating <= 5),
    comment TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```