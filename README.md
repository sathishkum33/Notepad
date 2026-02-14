import { api, setAccessToken } from "./axios";
import type { LoginRequest, LoginResponse } from "../types/auth";

export const loginUser = async (
  credentials: LoginRequest
): Promise<LoginResponse> => {
  const response = await api.post<LoginResponse>(
    "/login",
    credentials
  );

  setAccessToken(response.data.access_token);

  return response.data;
};

export const logoutUser = async (): Promise<void> => {
  await api.post("/logout");
  setAccessToken(null);
};