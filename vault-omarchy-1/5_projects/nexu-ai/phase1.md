### **Phase 1: Foundation & Core Features (0-30 Days)**

#### **1. Infrastructure & Database (Week 1)**
* **[DB]** Init Postgres container & define initial Schema (Users, Generations, Credits).
* **[DB]** Setup Migration tool (e.g., `golang-migrate` or built-in GORM/Ent migration).

#### **2. Backend - Go (Week 1-2)**
* [x] **[BE]** Init Project Structure (Standard Go layout).
* [x] **[BE]** **Implement Authentication:**
    * Setup Google OAuth2 Config.
    * Create `POST /auth/google` handler.
    * Generate JWT/Session token upon success.
    * Middleware: `RequireAuth`.
* [x] **[BE]** **Enchance Authentication:**
	- Add GetUserByID to repository for efficient token validation
	- Implement refresh tokens for long-lived sessions
	- Add SameSite cookie attribute
	- Add logging for security events (failed logins, etc.)
	- Add token blacklisting for logout
	- Add golangci-lint config
* **[BE]** **Implement Rate Limiting:**
    * Middleware to track request count per `ip` in Redis or Memory (since single node).
    * Rule: Max X requests per feature.
* **[BE]** **Gemini Integration:**
    * Create Service Wrapper for Gemini API calls.
    * Implement prompt templates for "Menu Creation" (structured JSON output).
    * Implement prompt templates for "Ad/Poster" (text + image prompt generation).
* **[BE]** **Feature APIs:**
    * `POST /api/v1/menu/generate`: Input ingredients/style -> Return menu text.
    * `POST /api/v1/ads/generate`: Input product/audience -> Return ad copy & visual description.
    * `GET /api/v1/history`: List user's past generations.

#### **3. Frontend - React (Week 2-3)**
* **[FE]** Init React Project (Vite + TailwindCSS recommended for speed).
* **[FE]** Setup Router (React Router) & Axios Interceptors (for JWT).
* **[FE]** **Auth UI:**
    * Login Page with "Sign in with Google" button.
    * Handle redirect & token storage.
* **[FE]** **Dashboard:**
    * Sidebar navigation (Menu Creator, Ads Creator, History).
    * Credit/Usage counter display.
* **[FE]** **Menu Creator Feature:**
    * Input Form: Restaurant Name, Type, Dish list (text area).
    * Result View: Display formatted Menu (allow copy/edit).
* **[FE]** **Ads Creator Feature:**
    * Input Form: Product Name, Target Audience, Tone.
    * Result View: Display Ad Copy + Placeholder for Image (or generated image if API ready).

#### **4. Testing & Polish (Week 4)**
* **[BE]** Add logging (Zap or Logrus) for user activities (crucial for your 60-day stats).
* **[QA]** End-to-end test: Login -> Generate Menu -> Check Rate Limit.
* **[Deploy]** CI/CD pipeline (GitHub Actions) to push Docker image to VPS.
* **[Launch]** Dry run with internal team (Lâm & Tùng).

### **Quick Note on MVP scope**
For the "Image Generation" part of the Ads/Menu features: Since Gemini Pro is primarily text-multimodal (unless you use specific Imagen endpoints), you might need to:
1.  Use Gemini to generate the **prompt**.
2.  Pass that prompt to a cheap image generation API (like Stable Diffusion via API) **OR** just focus on **Text Content** for the first 30 days if budget is tight.

Let me know if you want to add these to your actual Reminder/Task list!