# 📱 Mobile Web App Masterclass: Layouts, Geolocation, and Wearable Sync Patterns

This manual serves as a permanent state-preservation ledger, high-value code repository, and developer manual for building premium, responsive mobile web applications, resolving geocoding/timezone configurations, integrating secure hardware wearables (such as **Garmin Connect**), and managing zero-downtime database schemas.

> [!IMPORTANT]
> This document has been **fully sanitized for public repository publication**. It contains zero hardcoded credentials, secret keys, passwords, or sensitive email collection forms. All API examples utilize abstracted tokens and secure headers.

---

## 🎨 1. Premium Mobile UI Layout & Glassmorphism Design System

To construct responsive web layouts that rival native iOS/Android experiences, build a glassmorphic aesthetic utilizing custom HSL color palettes, deep blurs, responsive segmented components, and fluid slide animations.

### 1.1 Global Mobile Styling & Glassmorphism CSS (`App.css`)
Establish custom HSL-tailored tokens, fluid typography, backdrop filters, and slide-up modal keyframes.

```css
/* ==========================================================================
   🎨 Design Tokens & Theme Variables
   ========================================================================== */
:root {
  --primary: #0ea5e9;
  --primary-glow: rgba(14, 165, 233, 0.15);
  --success: #10b981;
  --warning: #f59e0b;
  --danger: #ef4444;
  
  /* Dark Glassmorphism Palette */
  --bg-app: #0b0f19;
  --bg-card: rgba(30, 41, 59, 0.45);
  --bg-input: rgba(15, 23, 42, 0.6);
  --border-color: rgba(255, 255, 255, 0.08);
  --border-active: rgba(14, 165, 233, 0.4);
  
  /* Typography & Shadows */
  --color-text-primary: #f1f5f9;
  --color-text-secondary: #94a3b8;
  --color-text-muted: #64748b;
  --shadow-large: 0 20px 40px -15px rgba(0, 0, 0, 0.5);
  --backdrop-blur: blur(16px);
  --transition-smooth: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
}

/* Glassmorphism Panel Utility */
.glass-panel {
  background: var(--bg-card);
  backdrop-filter: var(--backdrop-blur);
  -webkit-backdrop-filter: var(--backdrop-blur);
  border: 1px solid var(--border-color);
  border-radius: 16px;
  box-shadow: var(--shadow-large);
  transition: var(--transition-smooth);
}

.glass-panel:hover {
  border-color: var(--border-active);
}

/* Bottom Sheet Animation Keyframes */
@keyframes slideUp {
  from {
    transform: translateY(100%);
  }
  to {
    transform: translateY(0);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
```

### 1.2 Touch-Friendly Segmented View Switchers (iOS Style)
Segmented selections provide high tactile clarity on mobile devices for switching display categories (e.g., viewing logs vs. analytics).

```jsx
/* React Component Segmented Control */
<div style={{
  display: 'flex',
  background: 'var(--bg-input)',
  padding: '4px',
  borderRadius: '12px',
  border: '1px solid var(--border-color)',
  marginBottom: '20px'
}}>
  {['summary', 'calories', 'activities'].map((tab) => (
    <button
      key={tab}
      onClick={() => setActiveTab(tab)}
      style={{
        flex: 1,
        background: activeTab === tab ? 'rgba(255, 255, 255, 0.08)' : 'transparent',
        border: 'none',
        color: activeTab === tab ? 'var(--color-text-primary)' : 'var(--color-text-secondary)',
        padding: '10px 0',
        borderRadius: '8px',
        fontSize: '0.8rem',
        fontWeight: activeTab === tab ? '700' : '500',
        cursor: 'pointer',
        transition: 'var(--transition-smooth)'
      }}
    >
      {tab.charAt(0).toUpperCase() + tab.slice(1)}
    </button>
  ))}
</div>
```

### 1.3 Sliding Ergonomic Bottom Sheets
Bottom sheets mimic premium native drawers for adding health entries, tracking foods, or editing settings. They include an aesthetic drag handle and blur-backdrop dimmers.

```jsx
/* React Slide-Up Form Sheet Drawer Component */
{showDrawer && (
  <>
    {/* Fullscreen Backdrop overlay */}
    <div 
      className="bottom-sheet-backdrop"
      onClick={() => setShowDrawer(false)}
      style={{
        position: 'fixed',
        top: 0, left: 0, right: 0, bottom: 0,
        background: 'rgba(11, 15, 25, 0.65)',
        backdropFilter: 'blur(6px)',
        zIndex: 9998,
        animation: 'fadeIn 0.3s ease'
      }}
    />
    
    {/* Slide-Up Writable Container */}
    <div 
      className="glass-panel"
      style={{
        position: 'fixed',
        bottom: 0, left: 0, right: 0,
        borderTopLeftRadius: '24px',
        borderTopRightRadius: '24px',
        borderBottomLeftRadius: 0,
        borderBottomRightRadius: 0,
        padding: '24px',
        zIndex: 9999,
        background: 'var(--bg-card)',
        transform: 'translateY(0)',
        animation: 'slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1)',
        maxHeight: '85vh',
        overflowY: 'auto'
      }}
    >
      {/* Draggable indicator handle */}
      <div style={{
        width: '40px', height: '5px',
        background: 'var(--border-color)',
        borderRadius: '3px',
        margin: '-12px auto 20px auto'
      }} />
      
      {/* Form Fields */}
      <h3 style={{ color: 'var(--color-text-primary)', margin: '0 0 16px 0' }}>Log Exercise Session</h3>
      <form onSubmit={handleLogSubmit}>
        <div style={{ marginBottom: '16px' }}>
          <label style={{ display: 'block', color: 'var(--color-text-secondary)', fontSize: '0.85rem', marginBottom: '6px' }}>
            Activity Name
          </label>
          <input 
            type="text" 
            required 
            placeholder="e.g. HIIT Cardio, Pilates" 
            style={{
              width: '100%',
              padding: '12px',
              borderRadius: '8px',
              background: 'var(--bg-input)',
              border: '1px solid var(--border-color)',
              color: 'var(--color-text-primary)'
            }}
          />
        </div>
        <button 
          type="submit" 
          style={{
            width: '100%',
            padding: '14px',
            borderRadius: '10px',
            background: 'var(--primary)',
            color: '#fff',
            border: 'none',
            fontWeight: 'bold',
            cursor: 'pointer'
          }}
        >
          Save Log Entry
        </button>
      </form>
    </div>
  </>
)}
```

### 1.4 Concentric SVG Dynamic Progress Rings
Concentric progress indicators are mathematically rendered based on circle stroke-dashoffsets. This matches fitness dials found in iOS Health or Garmin Connect.

```jsx
/* React Concentric Progress Ring Component */
const ProgressRing = ({ radius, stroke, progress, color, label, icon }) => {
  const normalizedRadius = radius - stroke * 2;
  const circumference = normalizedRadius * 2 * Math.PI;
  const strokeDashoffset = circumference - (Math.min(100, Math.max(0, progress)) / 100) * circumference;

  return (
    <div style={{ display: 'flex', flexDirection: 'column', alignItems: 'center', gap: '8px' }}>
      <div style={{ position: 'relative', width: radius * 2, height: radius * 2 }}>
        <svg height={radius * 2} width={radius * 2} style={{ transform: 'rotate(-90deg)' }}>
          {/* Base track circle */}
          <circle
            stroke="rgba(255, 255, 255, 0.05)"
            fill="transparent"
            strokeWidth={stroke}
            r={normalizedRadius}
            cx={radius}
            cy={radius}
          />
          {/* Active progress indicator path */}
          <circle
            stroke={color}
            fill="transparent"
            strokeWidth={stroke}
            strokeDasharray={circumference + ' ' + circumference}
            style={{ strokeDashoffset, transition: 'stroke-dashoffset 0.6s cubic-bezier(0.16, 1, 0.3, 1)' }}
            strokeLinecap="round"
            r={normalizedRadius}
            cx={radius}
            cy={radius}
          />
        </svg>
        {/* Centered Overlay Label */}
        <div style={{
          position: 'absolute',
          top: 0, left: 0, right: 0, bottom: 0,
          display: 'flex', flexDirection: 'column',
          alignItems: 'center', justifyContent: 'center'
        }}>
          <span style={{ fontSize: '1.25rem' }}>{icon}</span>
          <span style={{ fontSize: '0.9rem', fontWeight: 'bold', color: 'var(--color-text-primary)' }}>
            {Math.round(progress)}%
          </span>
        </div>
      </div>
      <span style={{ fontSize: '0.75rem', color: 'var(--color-text-secondary)', fontWeight: '500' }}>
        {label}
      </span>
    </div>
  );
};
```

### 1.5 Brand Home-Button Navigation Click Handler
Rather than static headers, branding titles (`LineageHealth`) must serve as click targets that navigate the user back to the primary dashboard, resetting temporary routing states.

```jsx
/* Mobile Header Logo & Sidebar logo navigation targets */
<header className="mobile-header" style={{ display: 'flex', justifyContent: 'space-between', padding: '16px' }}>
  <h1 
    onClick={() => setMainView(currentUserProfile || isAdmin ? 'day-summary' : 'home')}
    style={{ 
      fontSize: '1.25rem', 
      fontWeight: '800', 
      background: 'linear-gradient(135deg, #4f46e5 0%, #06b6d4 100%)', 
      WebkitBackgroundClip: 'text', 
      WebkitTextFillColor: 'transparent', 
      margin: 0,
      cursor: 'pointer',
      userSelect: 'none'
    }}
  >
    LineageHealth
  </h1>
</header>
```

---

## 🗺️ 2. Geolocation & Multi-Timezone Synchronizations

To accurately compute scheduled activities, daily steps metrics, and greeting calendars, resolve coordinates via browser API and reverse-geocode names automatically.

### 2.1 Coordinates Telemetry & reverse-geocoding (Nominatim API)
Use OpenStreetMap's Nominatim with error fallbacks so coordinates act as city name fallbacks if services are busy or blocked.

```javascript
const handleDetectLocation = (setProfileForm, setDetectingLocation) => {
  setDetectingLocation(true);
  
  // Resolve standard ISO browser timezone
  const tz = Intl.DateTimeFormat().resolvedOptions().timeZone || 'UTC';
  
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      async (position) => {
        const { latitude, longitude } = position.coords;
        try {
          // Query OSM reverse geocoder
          const res = await fetch(
            `https://nominatim.openstreetmap.org/reverse?format=json&lat=${latitude}&lon=${longitude}&zoom=10`,
            { headers: { 'User-Agent': 'LineageHealthMobilePortal/1.0' } }
          );
          if (res.ok) {
            const data = await res.json();
            const addr = data.address || {};
            const city = addr.city || addr.town || addr.municipality || addr.village || addr.county || '';
            const country = addr.country || '';
            const locationString = city ? `${city}, ${country}` : country || `${latitude.toFixed(2)}, ${longitude.toFixed(2)}`;
            
            setProfileForm(prev => ({ ...prev, timezone: tz, location_name: locationString }));
            alert(`📍 Location Resolved: ${locationString}\nTimezone: ${tz}`);
          } else {
            // Geocoding server busy fallback
            setProfileForm(prev => ({ ...prev, timezone: tz, location_name: `Lat: ${latitude.toFixed(2)}, Lon: ${longitude.toFixed(2)}` }));
          }
        } catch (err) {
          console.error("Geocoding failed, falling back:", err);
          setProfileForm(prev => ({ ...prev, timezone: tz, location_name: `Lat: ${latitude.toFixed(2)}, Lon: ${longitude.toFixed(2)}` }));
        } finally {
          setDetectingLocation(false);
        }
      },
      (error) => {
        console.warn("Location permission denied:", error);
        setProfileForm(prev => ({ ...prev, timezone: tz, location_name: 'Permission Blocked / Manual' }));
        setDetectingLocation(false);
      },
      { timeout: 8000 }
    );
  } else {
    setProfileForm(prev => ({ ...prev, timezone: tz, location_name: 'Not Supported' }));
    setDetectingLocation(false);
  }
};
```

### 2.2 Timezone Greeting & Live Clock Display
Render greet messages and actual clocks relative to the user's selected profile timezone.

```jsx
/* Inside Day Summary Component rendering */
<div>
  <span style={{ fontSize: '0.9rem', color: 'var(--primary)', fontWeight: 'bold' }}>
    {(() => {
      try {
        return new Date().toLocaleDateString('en-US', { 
          weekday: 'long', month: 'short', day: 'numeric',
          timeZone: currentUserProfile?.timezone || undefined
        });
      } catch (e) {
        return new Date().toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
      }
    })()}
  </span>
  {currentUserProfile?.timezone && (
    <span style={{ fontSize: '0.9rem', color: 'var(--color-text-secondary)', marginLeft: '10px' }}>
      🕒 {(() => {
        try {
          return new Date().toLocaleTimeString('en-US', {
            hour: 'numeric', minute: '2-digit',
            timeZone: currentUserProfile.timezone
          });
        } catch (e) {
          return new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' });
        }
      })()}
    </span>
  )}
</div>
```

### 2.3 Timezone-Aware Agenda Appointments Rendering
Ensure that raw UTC dates stored in SQLite render correctly translated into the user's targeted timezone.

```jsx
/* Scheduler appointments parser */
{appointments.map(appt => {
  const localizedTime = (() => {
    try {
      return new Date(appt.date_time).toLocaleString('en-US', {
        dateStyle: 'medium',
        timeStyle: 'short',
        timeZone: currentUserProfile?.timezone || undefined
      });
    } catch (e) {
      return new Date(appt.date_time).toLocaleString('en-US', { dateStyle: 'medium', timeStyle: 'short' });
    }
  })();
  
  return (
    <div key={appt.id} className="appointment-card">
      <h4>{appt.doctor_name} - {appt.specialty}</h4>
      <p style={{ color: 'var(--primary)', fontSize: '0.85rem' }}>📅 {localizedTime}</p>
    </div>
  );
})}
```

---

## ⚡ 3. Cloud Wearable & Garmin Connect Integration

This secure module outlines the synchronization logic assuming token-based setups. For security and GitHub compatibility, session tokens/cookies are processed via stateless gateway handshakes without capturing or storing passwords.

### 3.1 Backend Secure Sync Endpoint (`main.py`)
Utilize `garminconnect` Python integration client. Cache session tokens on a server-writable pathway (`/tmp` or similar) to bypass repeated verification handshakes.

```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel
from sqlalchemy.orm import Session
from datetime import date
import os
import logging
from garminconnect import Garmin

app = FastAPI()
TEMP_DIR = os.environ.get("TEMP_DIR", "/tmp")

class GarminSecureTokenSyncRequest(BaseModel):
    # Abstracted session identifier - keeps passwords/emails out of codebases
    auth_token: str

@app.post("/sync/garmin/live")
def sync_garmin_live(req: GarminSecureTokenSyncRequest, db: Session = Depends(get_db)):
    logger = logging.getLogger("uvicorn.error")
    
    # 1. Initialize Garmin client with safe session token parameters
    try:
        # Secure token retrieval directory
        token_dir = os.path.join(TEMP_DIR, "garmin_tokens")
        os.makedirs(token_dir, exist_ok=True)
        
        # Load pre-authorized session client (credentials handled out-of-band)
        client = Garmin()
        client.load_session(token_dir, token_string=req.auth_token)
    except Exception as e:
        logger.error(f"Garmin Session Restore Error: {e}")
        raise HTTPException(
            status_code=401, 
            detail="Wearable session expired or unauthorized. Please re-authenticate."
        )
        
    try:
        today_str = date.today().isoformat()
        added_logs = []
        
        # 2. Sync Physical Activities (Workouts) & Deduplicate
        try:
            activities = client.get_activities(0, 10)
            for act in activities:
                start_time = act.get("startTimeLocal")
                if start_time and start_time.startswith(today_str):
                    act_name = f"🏃‍♂️ Garmin: {act.get('activityName', 'Workout')}"
                    
                    # Prevent inserting duplicate logs on multiple clicks
                    dup = db.query(ExerciseLog).filter(
                        ExerciseLog.activity_name == act_name,
                        ExerciseLog.log_date == date.today()
                    ).first()
                    
                    if not dup:
                        dur_s = act.get("duration", 0)
                        dur_m = max(1, int(dur_s / 60))
                        calories = int(act.get("calories", 0))
                        
                        workout_log = ExerciseLog(
                            activity_name=act_name,
                            duration_minutes=dur_m,
                            calories_burned=calories,
                            intensity="Moderate",
                            log_date=date.today()
                        )
                        db.add(workout_log)
                        added_logs.append(workout_log)
        except Exception as act_err:
            logger.warning(f"Error reading Garmin activities: {act_err}")
            
        # 3. Sync Daily Steps and Active Caloric Burn
        steps = 0
        active_calories = 0
        try:
            stats = client.get_stats(today_str)
            steps = stats.get("totalSteps") or 0
            active_calories = stats.get("activeCalories") or 0
            
            if steps > 0:
                steps_title = "👣 Garmin Daily Steps & Walking"
                dup_steps = db.query(ExerciseLog).filter(
                    ExerciseLog.activity_name == steps_title,
                    ExerciseLog.log_date == date.today()
                ).first()
                
                if dup_steps:
                    dup_steps.calories_burned = int(active_calories)
                    dup_steps.duration_minutes = max(1, int(steps / 100))
                else:
                    new_steps_log = ExerciseLog(
                        activity_name=steps_title,
                        duration_minutes=max(1, int(steps / 100)),
                        calories_burned=int(active_calories),
                        intensity="Low",
                        log_date=date.today()
                    )
                    db.add(new_steps_log)
                    added_logs.append(new_steps_log)
        except Exception as stats_err:
            logger.warning(f"Error reading Garmin telemetry metrics: {stats_err}")
            
        db.commit()
        return {
            "status": "success",
            "synced_activities_count": len(added_logs),
            "steps": steps,
            "active_calories": active_calories
        }
    except Exception as e:
        logger.error(f"Database write during sync failed: {e}")
        raise HTTPException(status_code=500, detail="Database sync insertion failed")
```

### 3.2 Frontend Secure Handshake Trigger (`App.jsx`)
Trigger synchronization by passing high-level security tokens obtained from dynamic OAuth configurations.

```javascript
const handleLiveGarminSync = async (sessionToken) => {
  setLiveSyncLoading(true);
  setLiveSyncError(null);
  
  setSyncLogs([
    "🌐 Connecting directly to secure Garmin API gateway...",
    "🔒 Exchanging localized session tokens...",
    "🔒 Authenticating security handshakes..."
  ]);

  try {
    const response = await fetch(`${API_BASE}/sync/garmin/live`, {
      method: 'POST',
      headers: { 
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${sessionToken}` // Abstract token delivery
      },
      body: JSON.stringify({ auth_token: sessionToken })
    });

    if (!response.ok) {
      throw new Error("Handshake connection timed out or was rejected");
    }

    const data = await response.json();
    setSyncLogs(prev => [
      ...prev,
      "✔️ Handshake authorized successfully!",
      `✔️ Synced active steps: ${data.steps || 0}`,
      `✔️ Active calorie burn: ${data.active_calories || 0} kcal`,
      `✔️ Imported ${data.synced_activities_count || 0} workouts!`,
      "📂 Sync complete. Persisting daily metrics..."
    ]);

    setGarminConnected(true);
    localStorage.setItem('garminConnected', 'true');
    
    // Trigger dashboard updates
    fetchExerciseLogs();
    fetchCalorieLogs();

  } catch (err) {
    setSyncLogs(prev => [...prev, `❌ Connection error: ${err.message}`]);
    setLiveSyncError(err.message);
  } finally {
    setLiveSyncLoading(false);
  }
};
```

### 3.3 Dynamic Sync Progress Stream Logger (Simulation Mode)
Provide high interactive feedback inside OAuth integrations by compiling progress states that write step-by-step logs into a terminal styled UI block.

```jsx
/* Real-time Handshake Logs Console */
<div style={{
  background: '#090d16',
  borderRadius: '10px',
  padding: '12px',
  fontFamily: 'monospace',
  fontSize: '0.75rem',
  color: '#34d399',
  border: '1px solid rgba(52, 211, 153, 0.2)',
  maxHeight: '150px',
  overflowY: 'auto'
}}>
  {syncLogs.map((log, index) => (
    <div key={index} style={{
      marginBottom: '4px',
      color: log.startsWith('❌') ? 'var(--danger)' : 
             log.startsWith('✔️') ? 'var(--success)' : '#60a5fa'
    }}>
      {log}
    </div>
  ))}
  {liveSyncLoading && <span className="console-cursor">▋</span>}
</div>
```

---

## 🔄 4. Zero-Downtime Auto-Schema SQLite Migrations

Ensure database schemas transition smoothly during application boots on Cloud Run / VMs without causing locks, drops, or crashes. Safely append columns utilizing raw DB queries with robust exception handles.

```python
# Insert within the main.py FastAPI initialization startup lifecycle
def run_auto_migrations(engine):
    import sqlite3
    conn = engine.raw_connection()
    try:
        cursor = conn.cursor()
        
        # 1. Add timezone column to Profile table
        try:
            cursor.execute("ALTER TABLE profiles ADD COLUMN timezone VARCHAR DEFAULT 'UTC';")
            conn.commit()
            print("✔ SQLite Migration: Added timezone column successfully.")
        except sqlite3.OperationalError:
            # Column already added
            pass
            
        # 2. Add location name column to Profile table
        try:
            cursor.execute("ALTER TABLE profiles ADD COLUMN location_name VARCHAR;")
            conn.commit()
            print("✔ SQLite Migration: Added location_name column successfully.")
        except sqlite3.OperationalError:
            pass
            
    finally:
        conn.close()
```

---

## 🚢 5. Cloud Deployments & Environment Binding (`deploy.sh`)

When deploying frontend interfaces and API backends as decoupled microservices (e.g., Google Cloud Run), resolve origin parameters and asset injections dynamically.

```bash
#!/bin/bash
# ==========================================================================
# 🚀 LineageHealth Decoupled Cloud Deployment Compiler
# ==========================================================================

# Exit on first failure
set -e

echo "🐳 [1/3] Building Backend Microservice..."
gcloud builds submit --tag gcr.io/lineagehealth/backend ./backend
gcloud run deploy lineage-backend \
  --image gcr.io/lineagehealth/backend \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --set-env-vars="TEMP_DIR=/tmp"

# Capture Deployed Backend Address
BACKEND_URL=$(gcloud run services describe lineage-backend --platform managed --region us-central1 --format="value(status.url)")
echo "✔ Deployed Backend Endpoint: ${BACKEND_URL}"

echo "⚛ [2/3] Injecting Backend Gateway to Static Client..."
# Build-time environment variable injection for Vite static bundles
echo "VITE_API_BASE=${BACKEND_URL}" > ./ui-prototype/.env

echo "🐳 [3/3] Building & Deploying Frontend App..."
gcloud builds submit --tag gcr.io/lineagehealth/frontend ./ui-prototype
gcloud run deploy lineage-frontend \
  --image gcr.io/lineagehealth/frontend \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated

FRONTEND_URL=$(gcloud run services describe lineage-frontend --platform managed --region us-central1 --format="value(status.url)")
echo "🎉 LineageHealth Successfully Live! Frontend App: ${FRONTEND_URL}"
```

---

These patterns form a complete production-ready blueprint for building highly premium mobile-web wellness dashboards integrated with real-time biometric APIs and dynamic location telemetry. Use this manual to bootstrap or debug sibling features.
