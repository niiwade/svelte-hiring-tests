<script lang="ts">
    interface FormData {
        firstName: string;
        lastName: string;
        email: string;
        password: string;
    }

    interface ErrorMessages {
        firstName: string | null;
        lastName: string | null;
        email: string | null;
        password: string | null;
    }

    interface SubmittedData {
        firstName: string;
        lastName: string;
        email: string;
    }

    let formData: FormData = {
        firstName: "",
        lastName: "",
        email: "",
        password: "",
    };
    let errors: ErrorMessages = {
        firstName: null,
        lastName: null,
        email: null,
        password: null,
    };
    let isSubmitted = false;
    let isSubmitting = false;
    let submittedData: SubmittedData | null = null;

    function oninput(event: Event) {
        const target = event.target as HTMLInputElement;
        formData = { ...formData, [target.id]: target.value };
        // Clear error when user starts typing
        errors[target.id as keyof ErrorMessages] = null;
        // Hide success message when form is being filled again
        if (isSubmitted) {
            isSubmitted = false;
        }
    }

    const validationRules = {
        firstName: {
            validate: (value: string): boolean => !!value?.trim(),
            message: "First Name is Required",
        },
        lastName: {
            validate: (value: string): boolean => !!value?.trim(),
            message: "Last Name is required",
        },
        email: {
            validate: (value: string): boolean =>
                !!(value && /\S+@\S+\.\S+/.test(value)),
            message: "Invalid email format",
        },
        password: {
            validate: (value: string): boolean =>
                !!(value && value.length >= 6),
            message: "Password must be at least 6 characters",
        },
    } as const;

    function validation(formData: FormData): boolean {
        let valid = true;
        errors = {
            firstName: null,
            lastName: null,
            email: null,
            password: null,
        };

        (Object.keys(validationRules) as (keyof FormData)[]).forEach(
            (field) => {
                if (!validationRules[field].validate(formData[field])) {
                    errors[field] = validationRules[field].message;
                    valid = false;
                }
            },
        );

        return valid;
    }

    async function onsubmit(event: Event) {
        event.preventDefault();
        isSubmitting = true;

        if (validation(formData)) {
            try {
                // Simulate API call
                await new Promise((resolve) => setTimeout(resolve, 1000));

                // Store submitted data (excluding password)
                submittedData = {
                    firstName: formData.firstName,
                    lastName: formData.lastName,
                    email: formData.email,
                };

                isSubmitted = true;
                
                // Reset form
                formData = {
                    firstName: "",
                    lastName: "",
                    email: "",
                    password: "",
                };

                // Reset form inputs
                const form = document.querySelector("form") as HTMLFormElement;
                form.reset();

            } catch (error) {
                console.error("Form submission failed:", error);
                submittedData = null;
            }
        }

        isSubmitting = false;
    }
</script>

<div class="form-container">
    <form {onsubmit}>
        <div class="form-group">
            <label for="firstName">First Name</label>
            <input
                id="firstName"
                type="text"
                placeholder="Enter your first name"
                {oninput}
                class:error={errors.firstName}
                value={formData.firstName}
            />
            {#if errors.firstName}
                <span class="error-message">{errors.firstName}</span>
            {/if}
        </div>

        <div class="form-group">
            <label for="lastName">Last Name</label>
            <input
                id="lastName"
                type="text"
                placeholder="Enter your last name"
                {oninput}
                class:error={errors.lastName}
                value={formData.lastName}
            />
            {#if errors.lastName}
                <span class="error-message">{errors.lastName}</span>
            {/if}
        </div>

        <div class="form-group">
            <label for="email">Email</label>
            <input
                id="email"
                type="email"
                placeholder="Enter your email"
                {oninput}
                class:error={errors.email}
                value={formData.email}
            />
            {#if errors.email}
                <span class="error-message">{errors.email}</span>
            {/if}
        </div>

        <div class="form-group">
            <label for="password">Password</label>
            <input
                id="password"
                type="password"
                placeholder="Enter your password"
                {oninput}
                class:error={errors.password}
                value={formData.password}
            />
            {#if errors.password}
                <span class="error-message">{errors.password}</span>
            {/if}
        </div>

        <button
            type="submit"
            class="submit-button"
            disabled={isSubmitting}
        >
            {isSubmitting ? 'Submitting...' : 'Submit'}
        </button>
    </form>

    {#if isSubmitted && submittedData}
        <div class="success-container">
            <div class="success-message" role="alert">
                <p>Form submitted successfully!</p>
            </div>
            
            <div class="submitted-data">
                <h3>Submitted Information:</h3>
                <dl>
                    <dt>First Name:</dt>
                    <dd>{submittedData.firstName}</dd>
                    
                    <dt>Last Name:</dt>
                    <dd>{submittedData.lastName}</dd>
                    
                    <dt>Email:</dt>
                    <dd>{submittedData.email}</dd>
                </dl>
            </div>
        </div>
    {/if}
</div>

<style>
    .form-container {
        max-width: 380px;
        margin: 0 auto;
        padding: 2rem;
        background-color: white;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .form-group {
        margin-bottom: 1.5rem;
    }

    label {
        display: block;
        margin-bottom: 0.5rem;
        font-weight: 500;
        color: #374151;
    }

    input {
        width: 100%;
        padding: 0.75rem;
        border: 1px solid #d1d5db;
        border-radius: 6px;
        font-size: 1rem;
        transition: border-color 0.15s ease-in-out;
    }

    input:focus {
        outline: none;
        border-color: #3b82f6;
        box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
    }

    input::placeholder {
        color: #9ca3af;
    }

    input.error {
        border-color: #ef4444;
    }

    .error-message {
        display: block;
        margin-top: 0.5rem;
        font-size: 0.875rem;
        color: #ef4444;
        background-color:burlywood;
    }

    .submit-button {
        width: 100%;
        padding: 0.75rem 1.5rem;
        background-color: #3b82f6;
        color: white;
        border: none;
        border-radius: 6px;
        font-size: 1rem;
        font-weight: 500;
        cursor: pointer;
        transition: background-color 0.15s ease-in-out;
    }

    .submit-button:hover {
        background-color: #2563eb;
    }

    .submit-button:focus {
        outline: none;
        box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.5);
    }

    .submit-button:disabled {
        background-color: #9ca3af;
        cursor: not-allowed;
    }

    .success-container {
        margin-top: 2rem;
        animation: fadeIn 0.3s ease-in-out;
    }

    .success-message {
        padding: 1rem;
        background-color: #edf7ed;
        border: 1px solid #c8e6c9;
        border-radius: 6px;
        color: #1b5e20;
        margin-bottom: 1.5rem;
    }

    .submitted-data {
        background-color: #f8fafc;
        border: 1px solid #e2e8f0;
        border-radius: 6px;
        padding: 1.5rem;
    }

    .submitted-data h3 {
        margin: 0 0 1rem 0;
        color: #374151;
        font-size: 1.1rem;
    }

    .submitted-data dl {
        margin: 0;
        display: grid;
        grid-template-columns: auto 1fr;
        gap: 0.5rem 1rem;
    }

    .submitted-data dt {
        color: #6b7280;
        font-weight: 500;
    }

    .submitted-data dd {
        margin: 0;
        color: #111827;
    }

    @keyframes fadeIn {
        from {
            opacity: 0;
            transform: translateY(-10px);
        }
        to {
            opacity: 1;
            transform: translateY(0);
        }
    }
</style>