# Error Handling Guidelines — Backend
version: 1.0
last-updated: 2026-06
changelog:
  - 1.0: initial version

## When to use this skill
When writing try/catch blocks, returning HTTP responses,
handling exceptions or defining error messages in backend code.

## Why
Consistent error handling makes the API predictable for
the frontend, avoids leaking internal details to clients,
and keeps logs meaningful for debugging.

## Rules

### HTTP status codes — use them correctly

| Situation | Status |
|---|---|
| Success with data | 200 OK |
| Resource created | 201 Created |
| Validation failed | 400 Bad Request |
| Not authenticated / subscription expired | 401 Unauthorized |
| Authenticated but no permission | 403 Forbidden |
| Resource not found | 404 Not Found |
| Duplicate detected | 409 Conflict |
| Server error | 500 Internal Server Error |

✅
```csharp
if (debt == null) return NotFound(new { error = "Debt not found." });
if (request.Amount <= 0) return BadRequest(new { error = "Amount must be greater than zero." });
if (!merchant.IsActive) return Unauthorized(new { error = "Subscription expired." });
return Ok(new { success = true, data = result });
```

❌
```csharp
if (debt == null) return Ok(new { error = "not found" }); ← wrong status
if (!merchant.IsActive) return BadRequest(...); ← wrong status for auth
```

### Always return structured error responses
Error responses must always include an `error` field.

✅
```csharp
return BadRequest(new { error = "Amount must be greater than zero." });
return NotFound(new { error = "Debt not found." });
return Unauthorized(new { error = "Subscription expired. Please renew to continue." });
```

❌
```csharp
return BadRequest("Amount must be greater than zero."); ← plain string
return StatusCode(500); ← no message
```

### Bilingual error messages
The product targets Vietnamese merchants with an English fallback.
Use the `T()` helper from BaseController for bilingual messages.

✅
```csharp
return NotFound(new { error = T(
    "Không tìm thấy khoản nợ.",
    "Debt not found.") });
```

### Global exception handler
Never let unhandled exceptions reach the client.
The global exception handler in middleware catches them
and returns a generic 500 response.

Do NOT add try/catch everywhere — only where you need
specific error handling logic.

✅ Let the global handler catch unexpected errors:
```csharp
public async Task<IActionResult> ProcessAudio(...) {
    var (merchant, error) = await ValidateMerchantAsync(UserId);
    if (error != null) return error;

    var result = await _geminiService.ProcessAsync(audio);
    return Ok(result);
    // If _geminiService throws unexpectedly, global handler catches it
}
```

✅ Use try/catch only for expected failure scenarios:
```csharp
try {
    response = await _httpClient.PostAsync(geminiUrl, content);
} catch (HttpRequestException ex) {
    _logger.LogError(ex, "Gemini API unreachable");
    return StatusCode(503, new { error = "AI service temporarily unavailable." });
}
```

❌ Wrapping everything in try/catch:
```csharp
public async Task<IActionResult> GetHistory(...) {
    try {
        var debts = await _debtRepository.GetHistoryAsync(merchantId);
        return Ok(debts);
    } catch (Exception ex) {
        return StatusCode(500, new { error = ex.Message }); // leaks internals!
    }
}
```

### Never expose internal details in error responses
Do not return exception messages, stack traces or SQL errors to the client.

✅
```csharp
return StatusCode(500, new { error = "An unexpected error occurred. Please try again." });
```

❌
```csharp
return StatusCode(500, new { error = ex.Message }); // may expose DB schema
return StatusCode(500, new { error = ex.StackTrace }); // never!
```

### Retry logic for external services
External APIs (Gemini, Expo Push) can fail transiently.
Use exponential backoff with a maximum of 3 attempts.

✅
```csharp
var delays = new[] { 2000, 5000, 10000 };
for (int attempt = 0; attempt < 3; attempt++) {
    try {
        var result = await CallExternalServiceAsync();
        return result;
    } catch (Exception ex) when (attempt < 2) {
        await Task.Delay(delays[attempt]);
    }
}
// All retries exhausted — handle gracefully
```

### Background service errors
Never let a background service crash the application.
Wrap the execution loop in try/catch and log errors.

✅
```csharp
protected override async Task ExecuteAsync(CancellationToken ct) {
    while (!ct.IsCancellationRequested) {
        try {
            await ProcessNotificationsAsync();
        } catch (Exception ex) {
            _logger.LogError(ex, "Error in notification cycle");
            // Service continues running
        }
        await Task.Delay(TimeSpan.FromHours(1), ct);
    }
}
```

## Checklist
- [ ] HTTP status codes match the situation
- [ ] All error responses have an `error` field
- [ ] Bilingual messages use T() helper
- [ ] No exception messages exposed to client
- [ ] try/catch only where specific handling is needed
- [ ] External service calls have retry logic
- [ ] Background service loops have try/catch

## Meta — Evolution
If a new error scenario needs specific handling →
report with **[SKILL UPDATE SUGGESTED]** indicating:
- The scenario
- Proposed status code and response structure
- Whether it's an extension or correction
