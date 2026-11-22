---
name: bymevpn-windows
description: Improve and review the ByMeVPN Windows client while preserving its Rust/Tauri architecture and aligning its features and experience with the Android app. Use for Windows client bugs, VPN reliability or speed, protocol support, UI/UX parity, and focused verification.
---

# ByMeVPN Windows

## Goal and boundaries

Deliver focused, evidence-based improvements to the Windows client. Prioritize, in order: correct and secure VPN behavior; fast connection setup and sustained traffic; protocol and backend compatibility; clear, polished user experience. Preserve the existing Rust/Tauri stack and project architecture unless the user explicitly requests otherwise. Treat “bug-free,” “never disconnects,” and “fastest” as goals, never as claims that can be proved universally.

Use the Android repository as a read-only product reference. Make only requested changes in the Windows project; do not operate on an Android device, USB connection, or mobile app. Adapt layouts for desktop while preserving the Android app’s user-visible features, copy, localization, and expected behavior where the Windows platform permits.

## Required companion skills

At the start of every ByMeVPN Windows task, read and follow these user-required skills:

- `C:\Users\User\.agents\skills\ub-mode\SKILL.md` — make the smallest complete change, find the underlying cause, and avoid unnecessary work.
- `C:\Users\User\.agents\skills\hallmark\SKILL.md` — apply its craft, visual hierarchy, and interaction guidance whenever evaluating or changing the product UI. Keep the existing application’s design system and architecture; use any full redesign/site workflow only when the user explicitly asks for it.
- `C:\Users\User\.agents\skills\gestures\SKILL.md` — apply when a scoped interaction uses dragging, swiping, flinging, holding, or a dismissible sheet. Do not add gestures merely to imitate mobile behavior on desktop.

Load the skills each task, but apply only the parts relevant to that task. Do not let generic workflows expand the requested scope.

## Start with evidence

1. Confirm the active Windows repository and inspect its applicable `AGENTS.md` instructions and Git state. Preserve staged, unstaged, and untracked user work.
2. Read the relevant implementation and current error/log evidence before changing code. For parity or API questions, inspect the current Android and backend repositories as read-only references; verify their current contracts rather than relying on memory.
3. State the concrete failing behavior, the code path that causes it, and the smallest remedy supported by evidence. If failures share a lifecycle, routing, or data-contract cause, fix that common cause rather than layering symptom-specific workarounds.
4. Keep work bounded to the user’s request. Do not turn a bug fix into an app-wide rewrite or audit unless requested.

## VPN correctness, reliability, and speed

- Trace the actual connection path: entitlement/session request, selected server and configuration parsing, core/config validation, process startup, TUN/Wintun readiness, route and DNS changes, and verified traffic. Find which stage fails or consumes time before optimizing it.
- Optimize measured work on the connection critical path. Keep network calls, probing, parsing, and process control off the UI thread. Avoid redundant probes, serial waits, stale sockets, and work that blocks connection readiness. Do not trade tunnel correctness, encryption, or leak protection for a superficial speed improvement.
- Keep lifecycle transitions explicit and cancellable. Handle startup failure, disconnect, process exit, app shutdown, and recovery without orphaned core processes or stale routes. Restore routes, DNS, and proxy state on every failure/stop path, including recoverable crash handling where the application controls it.
- Ensure the VPN endpoint remains reachable through the physical gateway when full-tunnel routes are installed; prevent routing loops. Treat DNS routing and restoration as part of tunnel lifecycle. Never report protection as active from process status alone: require the project’s appropriate route, DNS, and real traffic/egress evidence.
- Do not hide failed checks, claim a connection is protected when verification is incomplete, silently downgrade security/protocol behavior, or automatically switch endpoints in a way that breaks the user’s selection. Show specific, actionable errors and preserve safe rollback on failure.
- For stability or speed reports, separate measured results from assumptions. Compare equivalent server, protocol, network, and test conditions before attributing a difference to the client. Never promise zero drops or maximum speed without repeatable evidence.

## Protocol and backend compatibility

- Treat backend and Android behavior as the source of truth for authorization, entitlements, server visibility, subscription/device limits, config fields, and error meanings. Preserve authentication and payment semantics; do not alter production data or trigger payments/trials as a debugging shortcut.
- Trace each claimed protocol end to end: input/link parsing, normalization and validation, generated core configuration, bundled core capability/version, and user-facing error handling. Include the transports and security options actually used by the upstream contract (for example VLESS Reality over TCP/gRPC/XHTTP and Hysteria 2) rather than assuming protocol-name support is sufficient.
- Maintain or update a compact protocol/support matrix when a change affects protocol coverage. Mark a protocol as supported only when the selected core accepts the generated configuration; distinguish config validation from a live server connection. Reject unsupported or malformed configurations early with a useful explanation rather than a generic connection failure.
- Do not add or upgrade a core, driver, or dependency without confirming the project’s packaging and compatibility requirements. Do not claim that a binary or Wintun component is current solely because it exists in the repository.

## Android feature parity and Windows UX

- For the screens and flows affected by the task, compare current Android behavior with the Windows implementation: loading, success, empty, error, disabled, and recovery states; server selection and refresh; account/subscription limits; settings; diagnostics; RU/EN text; and interaction feedback as applicable.
- Preserve feature behavior and meaning, while adapting density, navigation, keyboard/mouse operation, resizing, and motion for desktop. Keep animations restrained, responsive, and consistent with connection state; respect accessibility and reduced-motion settings where supported.
- Do not claim full parity from a repository skim. For broad parity work, use a scoped screen/flow checklist and identify unsupported Windows-specific capabilities explicitly. For a narrow fix, inspect only the affected flow.

## Efficient verification and stopping rule

- Choose the smallest check that can catch regression in the changed behavior: a focused unit/integration test, static check, or relevant build. Do not add tests that simply mirror the implementation or run broad suites without a concrete reason.
- Run a focused check once. If it fails, diagnose and fix its cause, then rerun that relevant check once. Do not repeat successful checks or pursue indefinite test/fix loops. If a live network, server, administrator privilege, driver, or account is unavailable, report that boundary and use safe offline evidence.
- Do not initiate a real VPN session, alter Windows routes/DNS/proxy, install drivers, invoke UAC, use the user’s account, or activate a subscription/trial unless the user explicitly authorizes that specific live action. When authorized, perform only the relevant bounded check and restore the connection state safely.
- Stop when the scoped defect is fixed and the minimum relevant verification passes, or when a concrete external blocker prevents that. Do not continue polishing unrelated areas. Report what changed, what was checked, and what remains unverified in concise terms.

## External actions

Do not commit, push, publish a release, send messages, or change external systems unless the user explicitly authorizes that action in the current applicable context. Treat prior broad goals as product direction, not automatic authorization for a new external action.
