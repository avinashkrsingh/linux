# Install Angular CLI 18 globally
npm install -g @angular/cli@18
^(main|(feature|hotfix)\/COFY-\d+|release\/COFY_\d{6}_S[12])$


# Create a new Angular project with standalone APIs
ng new downtime-app --standalone --style=css --routing=false
cd downtime-app

# Generate the downtime-banner component
ng generate component downtime-banner --standalone

# Overwrite component + app files
cat > src/app/downtime-banner/downtime-banner.component.ts <<'EOF'
import { Component, Input, OnInit, OnDestroy } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-downtime-banner',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './downtime-banner.component.html',
  styleUrls: ['./downtime-banner.component.css']
})
export class DowntimeBannerComponent implements OnInit, OnDestroy {
  @Input() endTime!: Date;
  isVisible = true;
  timeLeft: string = '';

  private intervalId: any;

  ngOnInit() {
    this.updateCountdown();
    this.intervalId = setInterval(() => this.updateCountdown(), 1000);
  }

  ngOnDestroy() {
    clearInterval(this.intervalId);
  }

  private updateCountdown() {
    const now = new Date().getTime();
    const distance = this.endTime.getTime() - now;

    if (distance <= 0) {
      this.isVisible = false;
      clearInterval(this.intervalId);
      return;
    }

    const minutes = Math.floor((distance / 1000 / 60) % 60);
    const seconds = Math.floor((distance / 1000) % 60);
    this.timeLeft = `${minutes}m ${seconds}s remaining`;
  }
}
EOF

cat > src/app/downtime-banner/downtime-banner.component.html <<'EOF'
<div *ngIf="isVisible" class="banner">
  <h3>⚠️ Scheduled Downtime</h3>
  <p>{{ timeLeft }}</p>
</div>
EOF

cat > src/app/downtime-banner/downtime-banner.component.css <<'EOF'
.banner {
  background: #fff3cd;
  color: #856404;
  border: 1px solid #ffeeba;
  padding: 12px;
  text-align: center;
  font-weight: bold;
  position: fixed;
  top: 0; left: 0; right: 0;
  z-index: 1000;
  box-shadow: 0 2px 6px rgba(0,0,0,0.2);
}
EOF

cat > src/app/app.component.ts <<'EOF'
import { Component } from '@angular/core';
import { DowntimeBannerComponent } from './downtime-banner/downtime-banner.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [DowntimeBannerComponent],
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  downtimeEnd = new Date(new Date().getTime() + 5 * 60 * 1000); // 5 min countdown
}
EOF

cat > src/app/app.component.html <<'EOF'
<app-downtime-banner [endTime]="downtimeEnd"></app-downtime-banner>

<div style="margin-top:100px; text-align:center;">
  <h1>🚀 Welcome to Downtime App</h1>
  <p>Your main content goes here...</p>
</div>
EOF

cat > src/main.ts <<'EOF'
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent)
  .catch(err => console.error(err));
EOF

# Run the app
ng serve
