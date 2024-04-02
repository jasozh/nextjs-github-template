# nextjs-github-template

An unopinionated Next.js web template for deploying to GitHub Pages bundling TypeScript, ESLint, and Tailwind CSS, utilizing atomic design.

## Getting started

You can recreate the repository yourself with the following steps:

1. Run `npx create-next-app@latest` and push to a new repository.

2. In GitHub, go to **Settings > Pages > Build and deployment > Source > GitHub Actions** and generate `nextjs.yml` by clicking on the Next.js workflow.

3. Replace your `nextConfig` with the following. This is not required for deployment since `nextjs.yml` handles static export for us, but it's useful because now you can use `yarn build` to test if static export works before pushing to GitHub.

   ```ts
   const nextConfig = {
     output: "export",
   };
   ```

4. Replace `yarn start` in `package.json` with `npx serve@latest out` to deploy the static files locally.

## Notes

By default, the site is available at the `https://<username>.github.io/nextjs-github-template` subdomain. Images will not load since they do not take the `nextjs-github-template` base path into account. To resolve this for all possible base paths, import the image directly.

```ts
import Image from "next/image";
import vercel_svg from "../../public/vercel.svg";

<Image
  src={vercel_svg} // instead of src="/vercel.svg"
  alt="Vercel Logo"
  className="dark:invert"
  width={100}
  height={24}
  priority
/>;
```

Dynamic routes are only somewhat supported in the Next.js app router with static export. You must generate a list of valid static parameters, and your component must be a server component (not a client component). For example, create `src/app/[userid]/page.tsx` and paste in the following:

```ts
import React from "react";

interface ProfilePageParams {
  params: { userid: string };
}

/** Return a list of `params` to populate the [slug] dynamic segment */
export const generateStaticParams = async () => {
  return [{ userid: "asdf" }];
};

const ProfilePage = ({ params }: ProfilePageParams) => {
  const userid = params.userid as string;

  return <p>Hello {userid}.</p>;
};

export default ProfilePage;
```

## CI/CD

The repository is configured with automatic monthly updates using Dependabot and automatic build testing before each pull request. The CI/CD pipeline consists of the following workflows:

- `nextjs.yml` automatically deploys the website to GitHub Pages on every push to the `main` branch.
- `pull_request.yml` runs a sanity check on every opened pull request to make sure the app still builds.
- `dependabot.yml` automatically updates versions on a monthly basis.
- `dependabot_auto_merge.yml` automatically merges pull requests by Dependabot if it passes the sanity check.

The following GitHub settings are enabled:

- **General > Allow auto-merge**
- **Branches > Branch protection > Require status checks to pass before merging (pull_request_build)**

## Useful links

- https://github.com/gregrickaby/nextjs-github-pages
- https://nextjs.org/docs/pages/building-your-application/deploying/static-exports
