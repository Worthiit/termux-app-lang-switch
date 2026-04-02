## Termux: Hardware Keyboard Language Fix

This version of Termux includes a specific patch to enable seamless language switching when using an external hardware keyboard and even on native termux without x11


# The Problem
In the standard Termux application, users often face an issue where system shortcuts for switching input languages (like Ctrl + Space) do not work while the terminal is focused.
Previously, if you needed to change your typing language, you had to leave Termux, open another app to switch the language, and then return to the terminal. This happened because Termux was intercepting those specific key presses instead of letting the Android system handle them.


# The Solution
*I have modified the terminal view logic to ensure that language switch events are passed directly to the operating system. This allows the system to change the keyboard layout immediately without requiring you to switch apps.*

# Technical Implementation:

*In the source file TerminalView.java, an additional condition was added to the input handler to detect KeyEvent.KEYCODE_LANGUAGE_SWITCH. When this key is pressed, the application now correctly triggers the system-level response.
if ```(mClient.onKeyDown(keyCode, event, mTermSession)) {
    invalidate();
    return true;
} else if (event.isSystem() && (!mClient.shouldBackButtonBeMappedToEscape() || keyCode != KeyEvent.KEYCODE_BACK)) {
    return super.onKeyDown(keyCode, event);
} else if (event.getAction() == KeyEvent.ACTION_MULTIPLE && keyCode == KeyEvent.KEYCODE_UNKNOWN) {
    mTermSession.write(event.getCharacters());
    return true;
} else if (keyCode == KeyEvent.KEYCODE_LANGUAGE_SWITCH) {
    return super.onKeyDown(keyCode, event);
}```

Build Details
 * Development Environment: Compiled using Android Studio Ladybug.
 * Workflow: The APK is generated through GitHub Actions for transparency and ease of access.
This change ensures a smoother experience for developers who rely on hardware keyboards and multiple languages.
