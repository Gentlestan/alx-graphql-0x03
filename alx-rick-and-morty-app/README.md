Application of GraphQL in React
📚 Project Overview

This project demonstrates how to integrate GraphQL in a React (Next.js) application using the Rick and Morty API.
You will learn how to set up a Next.js app with TypeScript, Tailwind CSS, ESLint, and Apollo Client for GraphQL queries.

🧭 Objectives

Initialize a Next.js project with TypeScript, ESLint, and Tailwind CSS.

Connect to a GraphQL endpoint using Apollo Client.

Fetch and display Rick and Morty episode data.

Practice pagination and reusable UI components.

⚙️ Project Setup

1. Create the project
   mkdir alx-graphql-0x01
   cd alx-graphql-0x01
   npx create-next-app@latest alx-rick-and-morty-app --typescript --eslint --tailwind
   cd alx-rick-and-morty-app

2. Install Dependencies
   npm install @apollo/client graphql
   npm install @types/graphql

3. Create GraphQL Client and Queries
   📁 Directory: graphql/
   apolloClient.ts
   import { ApolloClient, InMemoryCache, HttpLink } from "@apollo/client";

const client = new ApolloClient({
link: new HttpLink({
uri: "https://rickandmortyapi.com/graphql"
}),
cache: new InMemoryCache()
});

export default client;

queries.ts
import { gql } from "@apollo/client";

export const GET_EPISODES = gql`  query getEpisodes($page: Int, $filter: FilterEpisode) {
    episodes(page: $page, filter: $filter) {
      info {
        pages
        next
        prev
        count
      }
      results {
        id
        name
        air_date
        episode
      }
    }
  }`;

4. Configure Apollo Provider
   📁 File: pages/\_app.tsx
   import "@/styles/globals.css";
   import type { AppProps } from "next/app";
   import { ApolloProvider } from "@apollo/client";
   import client from "@/graphql/apolloClient";

export default function App({ Component, pageProps }: AppProps) {
return (
<ApolloProvider client={client}>
<Component {...pageProps} />
</ApolloProvider>
);
}

5. Run the App
   npm run dev

Then open your browser and visit:
👉 http://localhost:3000

🧩 Query the GraphQL Endpoint
Duplicate the project
cp -r alx-graphql-0x01 alx-graphql-0x02
cd alx-graphql-0x02/alx-rick-and-morty-app

1. Define Interfaces
   📁 Directory: interfaces/index.ts
   interface InfoProps {
   pages: number;
   next: number;
   prev: number;
   count: number;
   }

export interface EpisodeProps {
id: number;
name: string;
air_date: string;
episode: string;
info: InfoProps;
}

export type EpisodeCardProps = Pick<EpisodeProps, 'id' | 'name' | 'air_date' | 'episode'>;

2. Build the Episode List Page
   📁 File: pages/index.tsx
   import { useQuery } from "@apollo/client";
   import { GET_EPISODES } from "@/graphql/queries";
   import { EpisodeProps } from "@/interfaces";
   import EpisodeCard from "@/components/common/EpisodeCard";
   import { useEffect, useState } from "react";

const Home: React.FC = () => {
const [page, setPage] = useState<number>(1);
const { loading, error, data, refetch } = useQuery(GET_EPISODES, {
variables: { page }
});

useEffect(() => {
refetch();
}, [page, refetch]);

if (loading) return <h1>Loading...</h1>;
if (error) return <h1>Error</h1>;

const results = data?.episodes.results;
const info = data?.episodes.info;

return (
<div className="min-h-screen flex flex-col bg-gradient-to-b from-[#A3D5E0] to-[#F4F4F4] text-gray-800">
<header className="bg-[#4CA1AF] text-white py-6 text-center shadow-md">
<h1 className="text-4xl font-bold tracking-wide">Rick and Morty Episodes</h1>
<p className="mt-2 text-lg italic">Explore the multiverse of adventures!</p>
</header>

      <main className="flex-grow p-6">
        <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
          {results && results.map(({ id, name, air_date, episode }: EpisodeProps, key: number) => (
            <EpisodeCard id={id} name={name} air_date={air_date} episode={episode} key={key} />
          ))}
        </div>

        <div className="flex justify-between mt-6">
          <button
            onClick={() => setPage(prev => prev > 1 ? prev - 1 : 1)}
            className="bg-[#45B69C] text-white font-semibold py-2 px-6 rounded-lg shadow-lg hover:bg-[#3D9B80] transition duration-200 transform hover:scale-105"
          >
            Previous
          </button>
          <button
            onClick={() => setPage(prev => prev < info.pages ? prev + 1 : prev)}
            className="bg-[#45B69C] text-white font-semibold py-2 px-6 rounded-lg shadow-lg hover:bg-[#3D9B80] transition duration-200 transform hover:scale-105"
          >
            Next
          </button>
        </div>
      </main>

      <footer className="bg-[#4CA1AF] text-white py-4 text-center shadow-md">
        <p>&copy; 2024 Rick and Morty Fan Page</p>
      </footer>
    </div>

);
};

export default Home;

3. Create Episode Card Component
   📁 File: components/common/EpisodeCard.tsx
   import { EpisodeCardProps } from "@/interfaces";

const EpisodeCard = ({ id, name, air_date, episode }: EpisodeCardProps) => {
return (
<div key={id} className="bg-white cursor-pointer shadow-md rounded-lg p-4 m-4 transition-transform duration-200 hover:scale-105">
<div className="flex justify-between items-center">
<h2 className="text-xl font-semibold text-gray-800">{name}</h2>
<span className="border px-2 text-xs rounded-full bg-blue-600 text-white flex items-center">{episode}</span>
</div>
<p className="text-gray-600">{air_date}</p>
</div>
);
};

export default EpisodeCard;

🧠 Key Concepts Learned

Setting up Apollo Client in a Next.js app.

Writing and executing GraphQL queries.

Using TypeScript interfaces for strong typing.

Structuring reusable React components.

Handling pagination with GraphQL data.

🧾 Repository Structure
alx-graphql-0x02/
└── alx-rick-and-morty-app/
├── components/
│ └── common/
│ └── EpisodeCard.tsx
├── graphql/
│ ├── apolloClient.ts
│ └── queries.ts
├── interfaces/
│ └── index.ts
├── pages/
│ ├── \_app.tsx
│ └── index.tsx
├── styles/
│ └── globals.css
└── README.md

🧑‍💻 Author

Name: Onyebuchi Ohazulike
Project: ALX GraphQL — Rick and Morty App
Cohort: ALX Software Engineering Program
