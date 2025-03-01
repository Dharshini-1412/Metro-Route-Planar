# Metro-Route-Planar

import java.util.*;

class MetroRoutePlanner {
    static class Edge {
        int dest, weight;

        Edge(int dest, int weight) {
            this.dest = dest;
            this.weight = weight;
        }
    }

    static class Graph {
        private final int stations;
        private final Map<Integer, String> stationNames;
        private final List<List<Edge>> adjList;

        Graph(int stations) {
            this.stations = stations;
            stationNames = new HashMap<>();
            adjList = new ArrayList<>(stations);
            for (int i = 0; i < stations; i++) {
                adjList.add(new ArrayList<>());
            }
        }

        void addStationName(int index, String name) {
            stationNames.put(index, name);
        }

        void addRoute(int src, int dest, int weight) {
            adjList.get(src).add(new Edge(dest, weight));
            adjList.get(dest).add(new Edge(src, weight));
        }

        void displayMetroNetwork() {
            System.out.println("\n Metro Network:");
            for (int i = 0; i < stations; i++) {
                System.out.print(stationNames.get(i) + " (" + i + ") -> ");
                for (Edge edge : adjList.get(i)) {
                    System.out.print(stationNames.get(edge.dest) + " (" + edge.weight + " min), ");
                }
                System.out.println();
            }
        }

        void dijkstraShortestPath(int start, int end) {
            int[] distances = new int[stations];
            int[] parent = new int[stations];
            boolean[] visited = new boolean[stations];

            Arrays.fill(distances, Integer.MAX_VALUE);
            Arrays.fill(parent, -1);
            distances[start] = 0;

            PriorityQueue<Edge> pq = new PriorityQueue<>(Comparator.comparingInt(e -> e.weight));
            pq.add(new Edge(start, 0));

            while (!pq.isEmpty()) {
                int u = pq.poll().dest;
                if (visited[u]) continue;
                visited[u] = true;

                for (Edge edge : adjList.get(u)) {
                    int v = edge.dest, weight = edge.weight;
                    if (!visited[v] && distances[u] + weight < distances[v]) {
                        distances[v] = distances[u] + weight;
                        parent[v] = u;
                        pq.add(new Edge(v, distances[v]));
                    }
                }
            }

            printPath(start, end, parent, distances[end]);
        }

        private void printPath(int start, int end, int[] parent, int time) {
            List<Integer> path = new ArrayList<>();
            for (int at = end; at != -1; at = parent[at]) {
                path.add(at);
            }
            Collections.reverse(path);

            System.out.println("\n️ Shortest Path using Dijkstra:");
            for (int i = 0; i < path.size(); i++) {
                System.out.print(stationNames.get(path.get(i)));
                if (i != path.size() - 1) System.out.print(" ➝ ");
            }
            System.out.println("\n Estimated Travel Time: " + time + " minutes");
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter number of stations: ");
        int stations = scanner.nextInt();
        Graph metro = new Graph(stations);

        System.out.println("Enter station names:");
        for (int i = 0; i < stations; i++) {
            System.out.print("Station " + i + ": ");
            String name = scanner.next();
            metro.addStationName(i, name);
        }

        System.out.print("Enter number of routes: ");
        int routes = scanner.nextInt();

        System.out.println("Enter the routes (start index, end index, travel time in minutes):");
        for (int i = 0; i < routes; i++) {
            int src = scanner.nextInt();
            int dest = scanner.nextInt();
            int weight = scanner.nextInt();
            metro.addRoute(src, dest, weight);
        }

        metro.displayMetroNetwork();

        System.out.print("\nEnter start station index: ");
        int start = scanner.nextInt();
        System.out.print("Enter destination station index: ");
        int end = scanner.nextInt();

        metro.dijkstraShortestPath(start, end);

        scanner.close();
    }
}

