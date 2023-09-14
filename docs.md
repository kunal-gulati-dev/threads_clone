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

## =============== Onboarding End ===============