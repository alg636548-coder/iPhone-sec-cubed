ContentView.swift

import SwiftUI
import AVFoundation
import Photos
import Contacts
import CoreLocation

struct ContentView: View {
    @State private var scanComplete = false
    @State private var report: [String] = []

    var body: some View {
        NavigationStack {
            List {
                Section {
                    Label(
                        "iPhone 17 Security Manager",
                        systemImage: "shield.lefthalf.filled"
                    )
                    .font(.headline)

                    Button("Scan My iPhone") {
                        scan()
                    }
                    .buttonStyle(.borderedProminent)
                }

                Section("Security Report") {
                    if !scanComplete {
                        Text("Tap Scan My iPhone to begin.")
                    } else {
                        ForEach(report, id: \.self) { item in
                            Text(item)
                        }
                    }
                }

                Section("Manual Security Checks") {
                    Link(
                        "Apple Certificate Trust Guide",
                        destination: URL(
                            string: "https://support.apple.com/102390"
                        )!
                    )

                    Link(
                        "Open iPhone Settings",
                        destination: URL(
                            string: UIApplication.openSettingsURLString
                        )!
                    )

                    Text(
                        "Review Settings > General > VPN & Device Management "
                        + "for configuration profiles."
                    )
                    .font(.footnote)
                }

                Section("Reported Trust Information") {
                    LabeledContent(
                        "Trust Store Version",
                        value: "2026073000"
                    )
                    LabeledContent(
                        "Trust Asset Version",
                        value: "1013"
                    )
                    Text(
                        "Recorded from user-provided information; "
                        + "not independently verified."
                    )
                    .font(.caption)
                }
            }
            .navigationTitle("Security Manager")
        }
    }

    private func scan() {
        report = [
            "Camera: " + cameraStatus(),
            "Microphone: " + microphoneStatus(),
            "Photos: " + photosStatus(),
            "Contacts: " + contactsStatus(),
            "Location: " + locationStatus(),
            "Profiles: Manual review required in Settings.",
            "System certificates: Full inventory unavailable to "
                + "ordinary iOS apps."
        ]
        scanComplete = true
    }

    private func cameraStatus() -> String {
        describe(AVCaptureDevice.authorizationStatus(for: .video))
    }

    private func microphoneStatus() -> String {
        describe(AVCaptureDevice.authorizationStatus(for: .audio))
    }

    private func photosStatus() -> String {
        switch PHPhotoLibrary.authorizationStatus(for: .readWrite) {
        case .authorized: return "Full access"
        case .limited: return "Limited access"
        case .denied: return "Denied"
        case .restricted: return "Restricted"
        case .notDetermined: return "Not requested"
        @unknown default: return "Unknown"
        }
    }

    private func contactsStatus() -> String {
        switch CNContactStore.authorizationStatus(for: .contacts) {
        case .authorized: return "Authorized"
        case .denied: return "Denied"
        case .restricted: return "Restricted"
        case .notDetermined: return "Not requested"
        case .limited: return "Limited access"
        @unknown default: return "Unknown"
        }
    }

    private func locationStatus() -> String {
        switch CLLocationManager().authorizationStatus {
        case .authorizedAlways: return "Always allowed"
        case .authorizedWhenInUse: return "While using"
        case .denied: return "Denied"
        case .restricted: return "Restricted"
        case .notDetermined: return "Not requested"
        @unknown default: return "Unknown"
        }
    }

    private func describe(
        _ status: AVAuthorizationStatus
    ) -> String {
        switch status {
        case .authorized: return "Authorized"
        case .denied: return "Denied"
        case .restricted: return "Restricted"
        case .notDetermined: return "Not requested"
        @unknown default: return "Unknown"
        }
    }
}
