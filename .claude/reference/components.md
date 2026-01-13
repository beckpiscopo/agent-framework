# Frontend/Components Reference

> React/Next.js patterns and conventions for this project

## Project Structure

```
frontend/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── layout.tsx          # Root layout
│   │   ├── page.tsx            # Home page
│   │   ├── (auth)/             # Auth route group
│   │   │   ├── login/page.tsx
│   │   │   └── register/page.tsx
│   │   ├── dashboard/          # Protected routes
│   │   │   ├── layout.tsx
│   │   │   └── page.tsx
│   │   └── api/                # API routes (if needed)
│   │
│   ├── components/
│   │   ├── ui/                 # Reusable UI primitives
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   └── ...
│   │   ├── forms/              # Form components
│   │   │   ├── login-form.tsx
│   │   │   └── ...
│   │   └── layout/             # Layout components
│   │       ├── header.tsx
│   │       ├── sidebar.tsx
│   │       └── ...
│   │
│   ├── hooks/                  # Custom React hooks
│   │   ├── use-auth.ts
│   │   └── ...
│   │
│   ├── lib/                    # Utilities and configs
│   │   ├── api.ts              # API client
│   │   ├── utils.ts            # Helper functions
│   │   └── validations.ts      # Zod schemas
│   │
│   ├── stores/                 # Zustand stores
│   │   └── auth-store.ts
│   │
│   └── types/                  # TypeScript types
│       └── index.ts
│
├── public/                     # Static assets
├── tailwind.config.ts
├── next.config.js
└── package.json
```

---

## Conventions

### Naming
- **Files:** `kebab-case.tsx` for components, `kebab-case.ts` for utilities
- **Components:** `PascalCase`
- **Hooks:** `use-kebab-case.ts` → `useKebabCase()`
- **Types:** `PascalCase`

### Component Structure
```tsx
// components/ui/button.tsx
import { cn } from "@/lib/utils"
import { ButtonHTMLAttributes, forwardRef } from "react"

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: "primary" | "secondary" | "ghost"
  size?: "sm" | "md" | "lg"
}

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant = "primary", size = "md", ...props }, ref) => {
    return (
      <button
        ref={ref}
        className={cn(
          "rounded-md font-medium transition-colors",
          // Variant styles
          variant === "primary" && "bg-blue-600 text-white hover:bg-blue-700",
          variant === "secondary" && "bg-gray-200 text-gray-900 hover:bg-gray-300",
          variant === "ghost" && "hover:bg-gray-100",
          // Size styles
          size === "sm" && "px-3 py-1.5 text-sm",
          size === "md" && "px-4 py-2",
          size === "lg" && "px-6 py-3 text-lg",
          className
        )}
        {...props}
      />
    )
  }
)
Button.displayName = "Button"
```

---

## Data Fetching with React Query

### Setup
```tsx
// lib/api.ts
const API_BASE = process.env.NEXT_PUBLIC_API_URL

export async function fetchApi<T>(
  endpoint: string,
  options?: RequestInit
): Promise<T> {
  const res = await fetch(`${API_BASE}${endpoint}`, {
    ...options,
    headers: {
      "Content-Type": "application/json",
      ...options?.headers,
    },
  })

  if (!res.ok) {
    const error = await res.json()
    throw new Error(error.detail || "API error")
  }

  return res.json()
}
```

### Query Pattern
```tsx
// hooks/use-user.ts
import { useQuery } from "@tanstack/react-query"
import { fetchApi } from "@/lib/api"
import { User } from "@/types"

export function useUser() {
  return useQuery({
    queryKey: ["user", "me"],
    queryFn: () => fetchApi<User>("/users/me"),
  })
}
```

### Mutation Pattern
```tsx
// hooks/use-login.ts
import { useMutation } from "@tanstack/react-query"
import { fetchApi } from "@/lib/api"

interface LoginInput {
  email: string
  password: string
}

export function useLogin() {
  return useMutation({
    mutationFn: (data: LoginInput) =>
      fetchApi("/auth/login", {
        method: "POST",
        body: JSON.stringify(data),
      }),
  })
}
```

---

## Forms with React Hook Form + Zod

```tsx
// components/forms/login-form.tsx
"use client"

import { useForm } from "react-hook-form"
import { zodResolver } from "@hookform/resolvers/zod"
import { z } from "zod"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { useLogin } from "@/hooks/use-login"

const loginSchema = z.object({
  email: z.string().email("Invalid email"),
  password: z.string().min(8, "Password must be at least 8 characters"),
})

type LoginFormData = z.infer<typeof loginSchema>

export function LoginForm() {
  const { mutate: login, isPending } = useLogin()

  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),
  })

  const onSubmit = (data: LoginFormData) => {
    login(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <Input
          type="email"
          placeholder="Email"
          {...register("email")}
        />
        {errors.email && (
          <p className="mt-1 text-sm text-red-500">{errors.email.message}</p>
        )}
      </div>

      <div>
        <Input
          type="password"
          placeholder="Password"
          {...register("password")}
        />
        {errors.password && (
          <p className="mt-1 text-sm text-red-500">{errors.password.message}</p>
        )}
      </div>

      <Button type="submit" disabled={isPending}>
        {isPending ? "Logging in..." : "Log in"}
      </Button>
    </form>
  )
}
```

---

## State Management with Zustand

```tsx
// stores/auth-store.ts
import { create } from "zustand"
import { persist } from "zustand/middleware"

interface AuthState {
  token: string | null
  setToken: (token: string | null) => void
  logout: () => void
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      token: null,
      setToken: (token) => set({ token }),
      logout: () => set({ token: null }),
    }),
    {
      name: "auth-storage",
    }
  )
)
```

---

## Protected Routes

```tsx
// components/auth-guard.tsx
"use client"

import { useAuthStore } from "@/stores/auth-store"
import { useRouter } from "next/navigation"
import { useEffect } from "react"

export function AuthGuard({ children }: { children: React.ReactNode }) {
  const token = useAuthStore((s) => s.token)
  const router = useRouter()

  useEffect(() => {
    if (!token) {
      router.push("/login")
    }
  }, [token, router])

  if (!token) return null

  return <>{children}</>
}
```

---

## Utility: cn() for class merging

```tsx
// lib/utils.ts
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```
