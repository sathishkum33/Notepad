import axios, {
  AxiosError,
  InternalAxiosRequestConfig,
} from "axios";
import type { RefreshResponse } from "../types/auth";

const API_BASE_URL = "http://localhost:8000";

export const api = axios.create({
  baseURL: API_BASE_URL,
  withCredentials: true, // required for HttpOnly refresh cookie
});

// In-memory token
let accessToken: string | null = null;

export const setAccessToken = (token: string | null) => {
  accessToken = token;
};

export const getAccessToken = () => accessToken;

/**
 * REQUEST INTERCEPTOR
 * Attach access token to every request
 */
api.interceptors.request.use(
  (config: InternalAxiosRequestConfig) => {
    if (accessToken) {
      config.headers.set("Authorization", `Bearer ${accessToken}`);
    }
    return config;
  }
);

/**
 * RESPONSE INTERCEPTOR
 * Handle token refresh
 */
api.interceptors.response.use(
  (response) => response,
  async (error: AxiosError) => {
    const originalRequest = error.config as
      | (InternalAxiosRequestConfig & { _retry?: boolean })
      | undefined;

    if (
      error.response?.status === 401 &&
      originalRequest &&
      !originalRequest._retry
    ) {
      originalRequest._retry = true;

      try {
        // IMPORTANT: use plain axios to avoid infinite loop
        const response = await axios.post<RefreshResponse>(
          `${API_BASE_URL}/refresh`,
          {},
          { withCredentials: true }
        );

        const newAccessToken = response.data.access_token;

        setAccessToken(newAccessToken);

        // update header safely
        originalRequest.headers.set(
          "Authorization",
          `Bearer ${newAccessToken}`
        );

        return api(originalRequest);
      } catch (refreshError) {
        setAccessToken(null);
        window.location.href = "/login";
        return Promise.reject(refreshError);
      }
    }

    return Promise.reject(error);
  }
);