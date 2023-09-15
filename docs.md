## Lets Start the project with next js 13

# After that we will install some packages into the project.
1. @clerk/nextjs => for authentication
2. @uploadthing/react => to upload our profile images.
3. @mongoose => for database transactions.
4. svix => for the webhooks.
5. uploadthing => this is for react package.

After installing the packages run the server.

# Next Steps to setup the css for the project.
1. Copy the code from the gist provided.
2. We will be facing the error "The `bg-dark-2` class does not exist.", So to solve this error, because it is a custom class, So we have to define it inside tailwind css.
3. Go to tailwind.config.js file and make the changes.
4. After replacing the code in the tailwind.config.js file you have to restart the server.
5. we will have another error that says "Cannot find module tailwindcss-animate", to solve this error we have to install a package becuase this is for tailwind animation.
6. npm install tailwindcss-animate

## Now lets improve our file and folder structure to ensure it is scalable.
1. We will be using advance next js 13 properties.
2. We will be using Route Groups.
3. Normally if we declare a folder it will include in the url path but with Route Groups we can make them to not include in the url path. for refrence https://nextjs.org/docs/app/building-your-application/routing/route-groups

4. So we will be making two folders (root) which will include all the project and (auth) which will include all the auth structure.
5. And inside (auth) directory create Three folders sign-in, sign-up, onboarding.
6. Create a layout.tsx file for auth module.

## Auth Flow

``` 
import { ClerkProvider } from "@clerk/nextjs";
import { Inter } from "next/font/google";

import "../globals.css";

export const metadata = {
    title: "Threads",
    description: "A Next.js 13 Meta Threads Application"
}

const inter = Inter({subsets: ["latin"]})

export default function RootLayout({children} : {children: React.ReactNode}) {
    return (
        <ClerkProvider>
            <html lang="en">
                <body className={`${inter.className} bg-dark-1`}>
                    {children}
                </body>
            </html>
        </ClerkProvider>
    )
}
```

=> Create a page.tsx file in onboarding directory, after that we will be getting clerk error.

## Clerk Setup

1. Create an account and create an application on the clerk portal and after that copy the keys provided be them and paste it in the .env.local file.
2. On Clerk platform enable organisation because we will have a feature for comunnities.
3. Follow the docs and paste the middleware code in the middleware.ts file which is in the root directory.
4. Add this code for the application in the middleware.
```
export default authMiddleware({
    publicRoutes: ['/', '/api/webhook/clerk'],
    ignoredRoutes: ['/api/webhook/clerk']
});
```
5. We have to build our own sign in and sign up pages as described in the clerk docs.
6. Update the environment variables as mentioned in docs.
```
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/onboarding
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/

```
7. Embed the user button that is going to allow me to manage my account information.

# Steps to test login
1. Go to localhost:3000/sign-in url and login in and Wrap the root layout.tsx file with ClerkProvider.


## =============== HomePage Structrure Start ===============

1. After removing the button component from the page.tsx file in the root directory, we will not be able to see text and we have to change the color of text but in layout.tsx (root).


2. Create component folder in the root directory, add three folders in it cards, forms, shared.
```

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
		<ClerkProvider>
			<html lang="en">
				<body className={inter.className}>
					<Topbar />
          
					<main>
						<LeftSidebar />
						<section className="main-container">
							<div className="w-full max-w-4xl">{children}</div>
						</section>
						<RightSidebar />
					</main>

					<Bottombar />
				</body>
			</html>
		</ClerkProvider>
  );
}
```
3. Create topbar, bottombar, leftsidebar, rightside bar components in the shared directory.

## Top Bar

1. Add logo and favicon icon in the topbar an layout
2. Make these changes in the file.

```
import Image from "next/image";
import Link from "next/link";
import { SignedIn, SignOutButton, OrganizationSwitcher } from "@clerk/nextjs";


function Topbar() {
	return (
		<nav className="topbar">
			<Link href="/" className="flex items-center gap-4">
				<Image src="/assets/logo.svg" alt="logo" width={28} height={28}/>
				<p className="text-heading3-bold text-light-1 max-xs:hidden">Threads</p>
			</Link>

			<div className="flex items-center gap-1">
				
				<div className="block md:hidden">
					<SignedIn>
						<SignOutButton>
							<div className="flex cursor-pointer">
								<Image src="/assets/logout.svg" alt="logout" width={24} height={24} />
							</div>
						</SignOutButton>
					</SignedIn>
				</div>

				<OrganizationSwitcher
					appearance={{
						elements: {
							organizationSwitcherTrigger: "py-2 px-4"
						}
					}}
				/>
			</div>
		</nav>
	)
}

export default Topbar;

```
## Left Side Bar
1. We have some constants in the constants file, map on those and add in  left sidebar component.
2. After applying we have a problem to find out which link is active, So we have to use import { usePathname, useRouter } from "next/navigation", this will tell us which link is active.
3. Add these hooks const router = useRouter(); const pathname = usePathname();
4. add these inside map and apply change in the link tag also


 ```

const isActive = (pathname.includes(link.route) && link.route.length> 1) || pathname === link.route;

<Link
    href={link.route}
    key={link.label}
    className={`leftsidebar_link ${isActive && 'bg-primary-500'}`}
>

```

5. Add Clerk Signin and out components too, use router.push in the signout button.
```
<div className="mt-10 px-6">
    <SignedIn>
        <SignOutButton signOutCallback={() => router.push('/sign-in')}>
            <div className="flex cursor-pointer gap-4 p-4">
                <Image
                    src="/assets/logout.svg"
                    alt="logout"
                    width={24}
                    height={24}
                />
                <p className="text-light-2 max-lg:hidden">Logout</p>
            </div>
        </SignOutButton>
    </SignedIn>
</div>
```

6. After implimenting this we have a scrollbar issue and have a big hollow white area we will fix it later.

## Bottom Bar
1. On website version we dont have any bottom bar, But it is for mobile version.
2. We will encouter a problem with big label names so we will use slice method of the string.
3. The code is given below for reference
```
function BottomBar() {
	const pathname = usePathname();
    return (
		<section className="bottombar">
			<div className="bottombar_container">
				{sidebarLinks.map((link) => {
					const isActive =
						(pathname.includes(link.route) &&
							link.route.length > 1) ||
						pathname === link.route;

					return (
						<Link
							href={link.route}
							key={link.label}
							className={`bottombar_link ${
								isActive && "bg-primary-500"
							}`}
						>
							<Image
								src={link.imgURL}
								alt={link.label}
								width={24}
								height={24}
							/>
							<p className="text-subtle-medium text-light-1 max-sm:hidden">
								{link.label.split(/\s+/)[0]}
							</p>
						</Link>
					);
				})}
			</div>
		</section>
	);
}

export default BottomBar;
```

## Right Side bar
1. If we start working on right sidebar initially it will be in the left side, because we have not provided class to main element in the layout.tsx
2. This is the code for reference

```
function RightSidebar() {
	return (
		<section className="custom-scrollbar rightsidebar">
			<div className="flex flex-1 flex-col justify-start">
				 <h3 className="text-heading4-medium text-light-1">Suggested Communities</h3>
			</div>
			<div className="flex flex-1 flex-col justify-start">
				 <h3 className="text-heading4-medium text-light-1">Suggested Users</h3>
			</div>
		</section>
	)
}

export default RightSidebar;

```
3. After that we can observe that the profile icon is not showing the text, So we have to install a new package called npm install @clerk/themes.
4. Made changes in the Topbar file.

```
import { dark } from "@clerk/themes";
<OrganizationSwitcher
    appearance={{
        baseTheme: dark, // new line of code.
        elements: {
            organizationSwitcherTrigger: "py-2 px-4"
        }
    }}
/>

```

## =============== HomePage Structrure End ===============

## =============== Onboarding Start ===============

Most of the platforms we work on have some onbarding like onboarding of a persom We will work on it this time.

## Onboarding page setup
1. All the authentication is done by clerk.
2. We will create a form for the user, So create a component in forms named AccountProfile.tsx.
3. So in this scenario we have two cases first we have to get data from clerk that a user is authenticated or not and we get those details from
import { currentUser } from "@clerk/nextjs";
4. Then we have to get the details of user from our mongodb also which we can requrest later.

5. Initial code for onboarding page.tsx

```
async function Page() {

    const user = await currentUser();

    const userInfo = {};


    const userData = {
        id: user?.id,
        objectId: userInfo?._id,
        username: userInfo?.username || user?.username,
        name: userInfo?.name || user?.firstName || "",
        bio: userInfo?.bio || "",
        image: userInfo?.image || user?.imageUrl,
    }


    return (
        <main className="mx-auto flex max-w-3xl flex-col justify-start px-10 py-20">
            <h1 className="head-text">Onboarding</h1>
            <p className="mt-3 text-base-regular text-light-2">Complete you profile now to use threads</p>
            <section className="mt-9 bg-dark-2 p-10">
                <AccountProfile
                    user={userData}
                    btnTitle="Contibue" 
                />
            </section>
        </main>
    )
}

export default Page;

```

6. Initial code for AccountProfile.tsx component.

```
"use client"

interface Props {
    user: {
        id: string;
        objectId: string;
        username: string;
        name: string;
        bio: string;
        image: string;
    }
    btnTitle: string;
}

const AccountProfile = ({user, btnTitle}: Props) => {
    return (
        <div>
            Account Profile
        </div>
    )
}

export default AccountProfile;

```

7. We have to use shadcn.ui library now.
8. Install shadcn ui library with command npx shadcn-ui@latest init, after it will ask come questions.
9. Install form ui with this command npx shadcn-ui@latest add form

10. Now its time to use the Form component provided by the shadcn, So we will use useform hook from react-hook-form and zod.
11. We will be using zod for our validations

12. To use zod we will create new folder in lib directory and create new file user.ts.
```
import * as z from 'zod';


export const UserValidation = z.object({
    profile_photo: z.string().url().nonempty(),
    name: z.string().min(3, {message: "minimum 3 characters"}).max(30),
    username: z.string().min(3).max(30),
    bio: z.string().min(3).max(1000),
})

```

13. We have to import all the form components and input components from the docs, add npx shadcn-ui@latest add input.

```
"use client"
import { useForm } from "react-hook-form";
import {
	Form,
	FormControl,
	FormDescription,
	FormField,
	FormItem,
	FormLabel,
	FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { zodResolver } from "@hookform/resolvers/zod";
import { UserValidation } from "@/lib/validations/user";
import * as z from "zod";
import { Button } from "../ui/button";



interface Props {
    user: {
        id: string;
        objectId: string;
        username: string;
        name: string;
        bio: string;
        image: string;
    }
    btnTitle: string;
}



const AccountProfile = ({user, btnTitle}: Props) => {

    const form = useForm({
		resolver: zodResolver(UserValidation),
        defaultValues: {
            profile_photo: "",
            name: "",
            username: "",
            bio: "",
        }
	});

     function onSubmit(values: z.infer<typeof UserValidation>) {
			// Do something with the form values.
			// ✅ This will be type-safe and validated.
			console.log(values);
		}

    return (
		<Form {...form}>
			<form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">
				<FormField
					control={form.control}
					name="username"
					render={({ field }) => (
						<FormItem>
							<FormLabel>Username</FormLabel>
							<FormControl>
								<Input placeholder="shadcn" {...field} />
							</FormControl>
							<FormDescription>
								This is your public display name.
							</FormDescription>
							<FormMessage />
						</FormItem>
					)}
				/>
				<Button type="submit">Submit</Button>
			</form>
		</Form>
	);
}


export default AccountProfile;

```

14. After applying this code we will have an issue with the light theme, So lets solve it.


15. After applying all the fields in the form this is the final code without styling.
```
<Form {...form}>
			<form
				onSubmit={form.handleSubmit(onSubmit)}
				className="flex flex-col justify-start gap-10"
			>
				<FormField
					control={form.control}
					name="profile_photo"
					render={({ field }) => (
						<FormItem className="flex items-center gap-4">
							<FormLabel className="account-form_image-label">
								{field.value ? (
									<Image
										src={field.value}
										alt="profile photo"
										width={96}
										height={96}
										priority
										className="rounded-full object-contain"
									/>
								) : (
									<Image
										src="/assets/profile.svg"
										alt="profile photo"
										width={24}
										height={24}
										className="object-contain"
									/>
								)}
							</FormLabel>
							<FormControl className="flex-1 text-base-semibold text-gray-200">
								<Input
									// placeholder="shadcn" {...field}
									type="file"
									accept="image/*"
									placeholder="Upload a photo"
									className="account-form_image-input"
									onChange={(e) =>
										handleImage(e, field.onChange)
									}
								/>
							</FormControl>
						</FormItem>
					)}
				/>

				<FormField
					control={form.control}
					name="name"
					render={({ field }) => (
						<FormItem className="flex items-center gap-3 w-full">
							<FormLabel className="text-base-semibold text-light-2">
								Name
							</FormLabel>
							<FormControl className="flex-1 text-base-semibold text-gray-200">
								<Input
									type="text"
									className="account-form_input no-focus"
									{...field}
								/>
							</FormControl>
						</FormItem>
					)}
				/>

				<FormField
					control={form.control}
					name="username"
					render={({ field }) => (
						<FormItem className="flex items-center gap-3 w-full">
							<FormLabel className="text-base-semibold text-light-2">
								Username
							</FormLabel>
							<FormControl className="flex-1 text-base-semibold text-gray-200">
								<Input
									type="text"
									className="account-form_input no-focus"
									{...field}
								/>
							</FormControl>
						</FormItem>
					)}
				/>

				<FormField
					control={form.control}
					name="bio"
					render={({ field }) => (
						<FormItem className="flex items-center gap-3 w-full">
							<FormLabel className="text-base-semibold text-light-2">
								Bio
							</FormLabel>
							<FormControl className="flex-1 text-base-semibold text-gray-200">
								<Textarea
                                rows={10}
									className="account-form_input no-focus"
									{...field}
								/>
							</FormControl>
						</FormItem>
					)}
				/>
				<Button type="submit">Submit</Button>
			</form>
		</Form>
```
16. After all of this we can se our styling is gone this is because when we install shadcn ui library the gloabal css file gets overridden.So copy again the global css file.

17. We might encounter the error related to hydration. this might occur because of the extension we are using on the browser.

18. Now lets work on the getting the default values of the singed in user.

```
const form = useForm({
		resolver: zodResolver(UserValidation),
        defaultValues: {
            profile_photo: user?.image || "",
            name: user?.name || "",
            username: user?.username || "",
            bio: user?.bio || "",
        }
	});

```
After making these changes next js will throw us with this error 
```
	Error: Invalid src prop (https://img.clerk.com/eyJ0eXBlIjoicHJveHkiLCJzcmMiOiJodHRwczovL2ltYWdlcy5jbGVyay5kZXYvb2F1dGhfZ29vZ2xlL2ltZ18yVk5PemNWNVZIUzRiZXpZejJRTmswNk1icmQucG5nIn0) on `next/image`, hostname "img.clerk.com" is not configured under images in your `next.config.js`
See more info: https://nextjs.org/docs/messages/next-image-unconfigured-host
```
Next js is protecting us 
In this case we are trying to render an image that is coming from clerk. clerk imediately confiured it once we log in using google, So now we need to allow img.clerk.com to allow post image on next js app.
And we do that in next.config file

```
/** @type {import('next').NextConfig} */
const nextConfig = {
	experimental: {
		serverActions: true,
		serverComponentsExternalPackages: ["mongoose"],
	},
	images: {
		remotePatterns: [
			{
				protocol: "https",
				hostname: "img.clerk.com",
			},
			{
				protocol: "https",
				hostname: "images.clerk.dev",
			},
			{
				protocol: "https",
				hostname: "uploadthing.com",
			},
			{
				protocol: "https",
				hostname: "placehold.co",
			},
		],
		typescript: {
			ignoreBuildErrors: true,
		},
	},
};

module.exports = nextConfig;
```
We have to above code for running this specially this line 
serverActions: true,
serverComponentsExternalPackages: ["mongoose"],

because after sometime we will be getting images from mongoose too.


19. Restart the server after making changes in the next.config file.

20. Now we have to work on photo uploading process because not all the users will be coming from google. some will be coming from normal sign up.

21. To handle Images and form data down will be the final code We have to create utils to check the image configuration. 
22. We will be using uploadthing now to upload the file, create a file in lib directory named uploadthing.ts.

23. After copying the code from uploadthing docs we need to create backend for this too and create api folder in app folder then create uploadthing folder in api folder and create two files core.ts and user.ts

```
import { createUploadthing, type FileRouter } from "uploadthing/next";
import { currentUser } from "@clerk/nextjs";

const f = createUploadthing();

const getUser = async () => await currentUser(); 





// FileRouter for your app, can contain multiple FileRoutes
export const ourFileRouter = {
	// Define as many FileRoutes as you like, each with a unique routeSlug
	media: f({ image: { maxFileSize: "4MB", maxFileCount: 1 } })
		// Set permissions and file types for this FileRoute
		.middleware(async ({ req }) => {
			// This code runs on your server before upload
			const user = await getUser();

			// If you throw, the user will not be able to upload
			if (!user) throw new Error("Unauthorized");

			// Whatever is returned here is accessible in onUploadComplete as `metadata`
			return { userId: user.id };
		})
		.onUploadComplete(async ({ metadata, file }) => {
			// This code RUNS ON YOUR SERVER after upload
			console.log("Upload complete for userId:", metadata.userId);

			console.log("file url", file.url);
		}),
} satisfies FileRouter;

export type OurFileRouter = typeof ourFileRouter;

```

```
import { createNextRouteHandler } from "uploadthing/next";

import { ourFileRouter } from "./core";

// Export routes for Next App Router
export const { GET, POST } = createNextRouteHandler({
	router: ourFileRouter,
});

```
24. After all this stuff we have the final frontend code for the account profile.

```
"use client"
import { useForm } from "react-hook-form";
import {
	Form,
	FormControl,
	FormDescription,
	FormField,
	FormItem,
	FormLabel,
	FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { zodResolver } from "@hookform/resolvers/zod";
import { UserValidation } from "@/lib/validations/user";
import * as z from "zod";
import { Button } from "../ui/button";
import Image from "next/image";
import { ChangeEvent, useState } from "react";
import { Textarea } from "../ui/textarea";
import { isBase64Image } from "@/lib/utils";
import { useUploadThing } from "@/lib/uploadthing";



interface Props {
    user: {
        id: string;
        objectId: string;
        username: string;
        name: string;
        bio: string;
        image: string;
    }
    btnTitle: string;
}



const AccountProfile = ({user, btnTitle}: Props) => {

    const [files, setFiles] = useState<File[]>([])
    const { startUpload } = useUploadThing("media")


    const form = useForm({
		resolver: zodResolver(UserValidation),
        defaultValues: {
            profile_photo: user?.image || "",
            name: user?.name || "",
            username: user?.username || "",
            bio: user?.bio || "",
        }
	});

    const handleImage = (e: ChangeEvent<HTMLInputElement>, fieldChange: (value: string) => void) => {
        e.preventDefault();

        const fileReader = new FileReader();

        if (e.target.files && e.target.files.length > 0) {
			const file = e.target.files[0];

            setFiles(Array.from(e.target.files))
            if (!file.type.includes('image')) return;

            fileReader.onload = async (event) => {
                const imageDataUrl = event.target?.result?.toString() || "";
                fieldChange(imageDataUrl)
            }
            fileReader.readAsDataURL(file);
		}
    }

    const onSubmit = async (values: z.infer<typeof UserValidation>) =>  {
        const blob = values.profile_photo;

        const hasImageChanged = isBase64Image(blob)

        if (hasImageChanged) {
            const imgRes = await startUpload(files)

            if (imgRes && imgRes[0].fileUrl) {
                values.profile_photo = imgRes[0].fileUrl;
            }
        }

        // TODO: Update user profile

    }

    return (
		<Form {...form}>
			<form
				onSubmit={form.handleSubmit(onSubmit)}
				className="flex flex-col justify-start gap-10"
			>
				<FormField
					control={form.control}
					name="profile_photo"
					render={({ field }) => (
						<FormItem className="flex items-center gap-4">
							<FormLabel className="account-form_image-label">
								{field.value ? (
									<Image
										src={field.value}
										alt="profile photo"
										width={96}
										height={96}
										priority
										className="rounded-full object-contain"
									/>
								) : (
									<Image
										src="/assets/profile.svg"
										alt="profile photo"
										width={24}
										height={24}
										className="object-contain"
									/>
								)}
							</FormLabel>
							<FormControl className="flex-1 text-base-semibold text-gray-200">
								<Input
									// placeholder="shadcn" {...field}
									type="file"
									accept="image/*"
									placeholder="Upload a photo"
									className="account-form_image-input"
									onChange={(e) =>
										handleImage(e, field.onChange)
									}
								/>
							</FormControl>
						</FormItem>
					)}
				/>

				<FormField
					control={form.control}
					name="name"
					render={({ field }) => (
						<FormItem className="flex flex-col w-full gap-3">
							<FormLabel className="text-base-semibold text-light-2">
								Name
							</FormLabel>
							<FormControl>
								<Input
									type="text"
									className="account-form_input no-focus"
									{...field}
								/>
							</FormControl>
						</FormItem>
					)}
				/>

				<FormField
					control={form.control}
					name="username"
					render={({ field }) => (
						<FormItem className="flex flex-col w-full gap-3">
							<FormLabel className="text-base-semibold text-light-2">
								Username
							</FormLabel>
							<FormControl>
								<Input
									type="text"
									className="account-form_input no-focus"
									{...field}
								/>
							</FormControl>
						</FormItem>
					)}
				/>

				<FormField
					control={form.control}
					name="bio"
					render={({ field }) => (
						<FormItem className="flex flex-col w-full gap-3">
							<FormLabel className="text-base-semibold text-light-2">
								Bio
							</FormLabel>
							<FormControl>
								<Textarea
									rows={10}
									className="account-form_input no-focus"
									{...field}
								/>
							</FormControl>
						</FormItem>
					)}
				/>
				<Button type="submit" className="bg-primary-500">
					Submit
				</Button>
			</form>
		</Form>
	);
}


export default AccountProfile;
```

## =============== Onboarding End ===============


## =============== Backend Start ===============

1. So for the backend we have to use server actions, So create a folder in lib folder named actions, create a file user.actions.ts in actions folder.

2. Create a new file in lib folder named mongoose.ts to connect mongodb database with the application.

```
import mongoose from 'mongoose';

let isConnected = false; // variable to check if mongoose is connected

export const connectToDB = async () => {
    mongoose.set("strictQuery", true);
    if (!process.env.MONGODB_URL) return console.log("MONGODB_URL not found")

    if (isConnected) return console.log("Already connected to MongoDB")

    try {
        await mongoose.connect(process.env.MONGODB_URL)
        isConnected = true
        console.log("connected to MongoDB")
    } catch (error) {
        console.log(error)
    }
}
```


3. We need to create models in the db to store data, So Create folder named models in lib folder, create user.model.ts file and write below code to create db table.

```
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
    id: {type: String, required: true},
    username: {type: String, required: true, unique: true},
    name: {type: String, required: true},
    image: String,
    bio: String,
    threads: [
        {
            type: mongoose.Schema.Types.ObjectId,
            ref: "Thread"
        }
    ],
    onboarded: {
        type: Boolean,
        default: false,
    },
    communities: [
        {
            type: mongoose.Schema.Types.ObjectId,
            ref: "Community"
        }
    ]
});


const User = mongoose.models.User || mongoose.model("User", userSchema)

export default User;
```

4. Now we have to make functions or actions to perform database operations in the user.actions.ts file.
```
"use server"

import { revalidatePath } from "next/cache";
import User from "../models/user.model";
import { connectToDB } from "../mongoose"

interface Params {
	userId: string;
	username: string;
	name: string;
	bio: string;
	image: string;
	path: string;
}



export async function updateUser({
    userId,
    username,
    name,
    bio,
    image,
    path
	}: Params): Promise<void> {
    connectToDB();

    try {
        await User.findOneAndUpdate(
			{ id: userId },
			{
				username: username.toLowerCase(),
				name,
				bio,
				image,
				onboarded: true,
			},
			{
				upsert: true,
			}
		);

		if (path === "/profile/edit") {
			revalidatePath(path);
		}
    } catch (error: any) {
        throw new Error(`Failed to create/update user: ${error.message}`)
    }
}
```
in above code what revalidate does is, It will reresh the data in the cache before the actual refresh time, So we will get the latest data everytime.

5. Now after that we have to make changes in AccountProfile.tsx file because now we have to integrate the update user api, So we have to use some hooks provided by next js and those are usePathname and useRouter for navigation after the successfull update of the user.

```
import { updateUser } from "@/lib/actions/user.actions";
import { usePathname, useRouter } from "next/navigation";

const router = useRouter();
const pathname = usePathname();

await updateUser({
		userId: user.id,
		username: values.username,
		name: values.name,
		bio: values.bio,
		image: values.profile_photo,
		path: pathname
	})
	if (pathname === "/profile/eidt") {
		router.back();
	}else {
		router.push("/")
}

```

Add above lines of code and submit the form it will add a user to the mongodb database.

## Create Thread Feature.

1. Create a folder inside (root) directory and create page.tsx file there.

2. Create an action for fetching the user info from the db.
```
export async function fetchUser(userId: string) {
	try {
		connectToDB();

		return await User.findOne({id: userId})
		// .populate({
		// 	path: 'communities',
		// 	model: Community 
		// })
	} catch (error: any) {
		throw new Error(`Failed to fetch user: ${error.message}`)
	}
}
```

3. Now on create thread page we need to create a form component, So create a PostThread.tsx file in components/forms folder.

4. After applying some of the code, we have to crate validation file in valiations folder named threads.ts.

5. Add fields in the PostThread form. The final code will be added later in the bottom.

6. Now we have to add the functionality to add threads, So create a new file in action folder named thread.action.ts.
7. Create models in the thread.model.ts file for threads.
```
import mongoose from "mongoose";

const threadSchema = new mongoose.Schema({
    text: {type: String, requred: true},
    author: {
        type: mongoose.Schema.Types.ObjectId,
        ref: "User",
        required: true,
    },
    community: {
        type: mongoose.Schema.Types.ObjectId,
        ref: "Community",
    },
    createdAt: {
        type: Date,
        default: Date.now
    },
    parentId: {
        type: String
    },
    children: [{
        type: mongoose.Schema.Types.ObjectId,
        ref: "Thread"
    }]
});

const Thread = mongoose.models.Thread || mongoose.model("Thread", threadSchema);

export default Thread;
```

8. thread.action.ts file code below
```
import Thread from "../models/thread.models";
import User from "../models/user.model";
import { connectToDB } from "../mongoose";
import { revalidatePath } from "next/cache";

interface Params {
    text: string;
    author: string;
    communityId: string | null;
    path: string;
}



export async function createThread({
    text, author, communityId, path
}: Params){
    connectToDB();
    const createdThread = await Thread.create({
        text, author, community: null, 
    });

    //  Update user model

    await User.findByIdAndUpdate(author, {
        $push: { threads: createThread._id }
    })

    revalidatePath(path);
}
```
9. We will be getting an error saying Threads is not defined, So how to solve it, To solve this issue we have to give "use server" at the top of file.
10. Why this happened, well thats because we can directly create database actions through the browser side.





## =============== Backend End ===============